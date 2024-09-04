---
title: "Understanding the Need for Virtual DOM"
datePublished: Sat Aug 31 2024 13:11:32 GMT+0000 (Coordinated Universal Time)
cuid: cm0i5w010000x0al133fycifw
slug: understanding-the-need-for-virtual-dom
tags: javascript, reactjs, dom-manipulation

---

## Introduction

When I started to get into frontend development I just wanted to learn how to learn react, I jumped straight in coding it out not knowing why react exists or what problem it solves. As a result I failed to appreciate the library. Now that I have worked on it for few years now, I am just trying to answer these questions for myself and documenting it for the future. For anyone trying to understand the need for React, I will try to explain my understanding about the library.

## What is a DOM

DOM stands for **Document Object Model** is a browsers representation of your HTML code as a tree structure. Each element in the HTML document is represented as a node in this tree. DOM also allows your JavaScript to interact with your HTML document, letting you to manipulate content.

### Steps involved in Paint of layout

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725451527293/6ef287d1-7003-4a47-b395-30aadab633d9.png align="left")

After data is fetched in chunks of 8kb a content tree is created by parsing the HTML document converting it into DOM nodes. Then a render tree is constructed by parsing the css and the content tree. After the render tree is constructed each node is given exact coordinates where it should appear on the screen. Then the back-end UI layer will be traversed and painted. For every small change the browser uses a dirty bit system. A DOM node that changes marks itself dirty and a incremental layout change is triggered. This incremental change is done by repaint and reflow.

#### Repaint

The Repaint occurs when changes are made to the appearance of the elements that change the visibility, but doesn't affect the layout.

#### Reflow

Reflow means re-calculating the positions and geometries of elements in the document. The Reflow happens when changes are made to the elements, that affect the layout of the partial or whole page. The Reflow of the element will cause the subsequent reflow of all the child and ancestor elements in the DOM.

Both Reflow and Repaints are an expensive operation.

## Virtual DOM (VDOM)

Virtual DOM is a lightweight in memory copy of the real DOM. It is a clever hack that some libraries use to make UI updates more efficient.

#### How is virtual DOM represented

React's virtual DOM implies a "**virtual**" representation (as a tree, as each element is a node that holds an object ) of a user interface, which is preserved in memory and synchronised with the browser's DOM via React's ReactDOM library.

Below is a code snippet for a basic React component. The component increments the value of the value of variable `count` by one.

```jsx
function App() {
 const [count, setCount] = useState(0);

 return (
   <div>
     <h1>Counter: {count}</h1>
     <button onClick={() => setCount(count + 1)}>Increment</button>
   </div>
 );
}
```

Below we can see how React converts to to a native JavaScript object.

```json
{
 "type": "div",
 "props": {},
 "children": [
   {
     "type": "h1",
     "props": {},
     "children": [
       {
         "type": "TEXT_ELEMENT",
         "props": {
           "nodeValue": "Counter: 0"
         }
       }
     ]
   },
   {
     "type": "button",
     "props": {
       "onClick": "setCount(count + 1)"
     },
     "children": [
       {
         "type": "TEXT_ELEMENT",
         "props": {
           "nodeValue": "Increment"
         }
       }
     ]
   }
 ]
}
```

### How virtual DOM is faster

As we have seen in the above sections that manipulating the DOM is a expensive operation when you are making a bunch of changes. This is because manipulating native JavaScript objects are more faster than manipulating the DOM. When a change is requested by the UI, it is first updated on the virtual DOM. Multiple changes are batched together and the smallest number of updates required to to synchronise the virtual DOM with the real DOM and applied. This is done by finding the difference difference in DOM and virtual DOM. This reduction in the number of operations is what makes React faster.

## Reconciliation using diffing of virtual DOM

When there are changes the component tree to reconcile nested components. It compares the component types, props, and keys to determine whether a component needs to be updated, added, or removed and creates another version of the virtual DOM. When there are two versions on Virtual DOM, it uses a diffing algorithm to identify the difference between them, trying to minimize the number of changes needed. The algorithm assumes elements of different types will result in different trees and elements that don't need to be checked can be set as static. If the root element type changes, then the old tree is discarded and a new one is built, effectively performing a full rebuild of the tree and the element type remains the same, React compares the attributes of both versions and updates only the nodes with changes, without altering the tree structure. The component will be updated in the next lifecycle call.

---

## Conclusion

React’s Virtual DOM provides a more efficient way to handle UI updates by minimizing the costly operations associated with manipulating the real DOM. By working with a lightweight, in-memory representation of the DOM, React can batch updates, find the minimal set of changes required, and apply them in an optimized manner. This approach not only improves performance but also simplifies the development process by abstracting the complexities of direct DOM manipulation. For developers, understanding this process can deepen their appreciation of React’s capabilities and why it’s a popular choice for building modern, dynamic web applications.

---

#### Links

[https://web.dev/articles/howbrowserswork#the\_main\_flow](https://web.dev/articles/howbrowserswork#the_main_flow) [https://www.geeksforgeeks.org/what-is-diffing-algorithm/](https://www.geeksforgeeks.org/what-is-diffing-algorithm/) [https://developer.mozilla.org/en-US/docs/Web/Performance/How\_browsers\_work](https://developer.mozilla.org/en-US/docs/Web/Performance/How_browsers_work) [https://dev.to/gopal1996/understanding-reflow-and-repaint-in-the-browser-1jbg](https://dev.to/gopal1996/understanding-reflow-and-repaint-in-the-browser-1jbg) [https://www.freecodecamp.org/news/what-is-the-virtual-dom-in-react/](https://www.freecodecamp.org/news/what-is-the-virtual-dom-in-react/) [https://refine.dev/blog/react-virtual-dom/#drawbacks-in-updating-the-dom](https://refine.dev/blog/react-virtual-dom/#drawbacks-in-updating-the-dom) [https://dev.to/geraldhamiltonwicks/understanding-diffing-algorithm-in-react-5581](https://dev.to/geraldhamiltonwicks/understanding-diffing-algorithm-in-react-5581)

---

##### Connect with me

* Email: [sebinsebzz2002@gmail.com](mailto:sebinsebzz2002@gmail.com)
    
* GitHub: [github.com/sebzz2k2](http://github.com/sebzz2k2)