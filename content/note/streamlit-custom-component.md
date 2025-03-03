---
title: How to Create a Streamlit Custom Component?
description: A guide to creating a Streamlit custom component with Web Components
date: 2025-03-02
tags:
  - web component
  - web development
  - streamlit
---

{% raw %}

# How to Create Streamlit Custom Component?

Streamlit's support for custom components is limited, and creating one using standard web technologies can be complex. The trickiest aspect is managing the event handling within Streamlit. However, here is a practical solution for developing a **Streamlit custom component** with **Web Components** that allows for **two-way communication** with the Streamlit app. You can find all the code for this working example on [GitHub](https://github.com/xiaohk/streamlit-component-example).

# 1. Compile Web Component

## 1.1. Component Overview

In this tutorial, we will walk through an example using Lit Element Web Component. The code of the component quest-board is in the directory `quest-board`.

The component is simple. It takes a string array `quests` as an attribute / property. It renders the quests. Each quest has a button where user can click to query the reward of the quest. When the button is clicked, the component will emit an event with a promise `resolve`. The component consumer is expected to resolve the promise with the reward associated with the quest.

Below is an example how we use this web component in HTML (`index.html`). It passes three quests to the component. When the component asks for a quest reward, it returns a random number as the reward.

```html
<body>
  <quest-board
    quests='["Echoes of the Forgotten Realm", "The Ashen King&apos;s Bargain", "The Clockwork Gambit"]'
  ></quest-board>
</body>

<script>
  const questBoard = document.querySelector('quest-board');
  questBoard.addEventListener('quest-clicked', event => {
    const { resolve, reject } = event.detail;
    const randomReward = Math.floor(Math.random() * 10000);
    resolve(randomReward);
  });
</script>
```

We can try out this component by:

```bash
cd quest-board
pnpm install
pnpm run dev
```

<img align="center" width="350px" src="https://github.com/user-attachments/assets/e6a6342f-61fd-444a-82d3-d2547fa6bb63">

Example interaction of the quest-board component.

## 1.2. Compile the Component to a JS File

We use Vite’s library mode to build the web component as a JS file. Consumers can import the JS file and use our component by writing HTML element `<quest-board></quest-board>`.

```bash
cd quest-board
pnpm install
pnpm run build:library
```

```json
# package.json: define the library entry point
"exports": {
  ".": {
    "import": {
      "types": "./lib/quest-board.d.ts",
      "default": "./lib/index.js"
    }
  }
},
```

# 2. Integrate Web Component into Streamlit

Download the official Streamlit custom component template from [GitHub](https://github.com/streamlit/component-template). We will use the react-less template.

## 2.1. Import Compiled Web Component

We add an NPM dependency of our web component from the template’s frontend NPM package.

```bash
cd quest_board_stremlit/frontend
pnpm add ../../../quest_board
pnpm install
```

Then, in `frontend/index.tsx` , we will add code to create the `<quest-board>` element and bind data properties as well as event handlers.

```tsx
import 'quest-board';

this.questBoardContainer = document.body.appendChild(
  document.createElement('div')
);
this.questBoardContainer.classList.add('quest-board-container');
this.questBoardElement = this.questBoardContainer.appendChild(
  document.createElement('quest-board')
);
```

## 2.2. Streamlit Component Development

Next, we willl create a Streamlit Component by building a wrapper that manages the data and events for our `quest-board` component. To view the Streamlit Component, we need to launch two servers:

**Frontend**

```bash
cd quest_board_stremlit/quest_board_stremlit/frontend
pnpm install
pnpm run start
```

**Backend**

```bash
cd quest_board_streamlit
pip install -e .
streamlit run quest_board_stremlit/example.py
```

# 3. Design Data and Event Flow

The most complex aspect is managing the data and event flow. Unlike a traditional browser's event-driven flow, Streamlit uses a declarative approach, meaning changes only appear in the app after calling `st.rerun()`. We'll divide the data and event flow into two sections: (1) between the **web component** and the Streamlit **wrapper frontend**, and (2) between the Streamlit **wrapper frontend** and the **backend**.

<img align="center" width="100%" src="https://github.com/user-attachments/assets/b5133265-28a1-4e6d-9d24-dbb4e9e362ec">

## 3.1. Web Component ↔ Wrapper Frontend

### **Data**

The wrapper frontend will pass the data to our web component through the typical HTML attributes or JavaScript component properties. This is implemented in `frontend/index.tsx`.

```tsx
this.questBoardElement.quests = data.args.quests;
```

### Event

The event flow is also similar to typical web apps. We declare event listeners to catch events coming from `<quest-board>` .

```tsx
this.questBoardElement.addEventListener('quest-clicked', event => {});
```

## 3.2. Wrapper Frontend ↔ Wrapper Backend

### Data

The data flows between Streamlit Python backend and JavaScript frontend through props. We define the props in `__init__.py` , and the JavaScript can access passed props through the special `Streamlit.RENDER_EVNT` event.

```python
# __init__.py
def quest_board(
    quests: list[str], event_responses: list[QuestBoardResponse], key: str | None = None
) -> dict[str, list[QuestBoardEvent]]:
```

```tsx
// frontend/index.tsx
Streamlit.events.addEventListener(Streamlit.RENDER_EVENT, (event: Event) => {
  questBoardComponent.onRender(event);
});

onRender(event: Event): void {
  // Get the RenderData from the event
  const data = (event as CustomEvent<RenderData<EuphonyComponentProps>>)
    .detail;
  const questsString = JSON.stringify(data.args.quests);
}
```

### Event

In Streamlit's wrapper backend, we can't create event listeners directly. Instead, we use the existing data communication channel to manage events. In Streamlit, a component can send a reactive value back to the backend, which we can use to include **event requests**. Once the backend processes these requests, we send the **event responses** back to the frontend using the same props.

```python
# example.py

# Create the component and its reactive component_value
component_value = quest_board(quests, st.session_state["event_responses"], key=key)

# "Event loop": continuously checking if there are new requests in component_value
# If there are new requests, handle them, and use st.rerun() to send the response
# back into the frontend
if component_value and component_value["events"]:
    # Clean the previous responses
    st.session_state["event_responses"] = []
    events_to_handle = []

    for e in component_value["events"]:
        if e["uniqueID"] not in st.session_state["handled_events"]:
            events_to_handle.append(e)

    # Handle the events
    tasks = []
    for e in events_to_handle:
        tasks.append(handle_event(e))

    await asyncio.gather(*tasks)

    if len(events_to_handle) > 0:
        st.rerun(scope="fragment")
```

```tsx
// frontend/index.tsx

// Helper function to send event requests to streamlit component value
_dispatchPendingEvent(
  eventName: string,
  detail: string,
  uniqueID: string,
  resolve: (value: any) => void,
  reject: (reason?: any) => void
) {
  this.pendingEvents.push({
    event: eventName,
    detail: detail,
    uniqueID
  });

  this.eventResolveMap.set(uniqueID, {
    resolve: resolve,
    reject: reject
  });

  if (this.eventDispatchTimer) {
    clearTimeout(this.eventDispatchTimer);
  }

	// There is a delay of Streamlit communication.
	// We stagger and batch events to prevent some events being missed
  this.eventDispatchTimer = window.setTimeout(() => {
    Streamlit.setComponentValue({
      events: this.pendingEvents
    });
  }, 300);
}

// Delegate the event handling of quest-clicked to Python
this.questBoardElement.addEventListener('quest-clicked', event => {
  const customEvent = event as CustomEvent<QuestClickedRequest>;
  const request = customEvent.detail;
  const uniqueID = crypto.randomUUID();
  this._dispatchPendingEvent(
    'quest-clicked',
    request.quest,
    uniqueID,
    request.resolve,
    request.reject
  );
});
```

After binding the data and events, we now have a working Streamlit component with two-way communication to our custom web component!

<img align="center" width="500px" src="https://github.com/user-attachments/assets/42c71fcc-1cb2-40c0-80da-64c4c00c061c">

Working example of our quest board web component as a streamlit custom component.

# 4. Use Streamlit Component in Streamlit App

Finally, we can distribute the component and use it in any Streamlit apps. To build a wheel and distribute the wheel, check out the [official guide](https://docs.streamlit.io/develop/concepts/custom-components/publish). To import this Streamlit component, follow the same pattern as in the `example.py`. Each app can implement its own logic to handle the events coming from the `quest-board` component.

{% endraw %}
