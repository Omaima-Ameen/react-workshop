## React Work Loop Internals
**(Placed after the Concurrent Rendering section)**

-----------------------------------------------------------


The introduction of Concurrent Rendering enables React to pause, resume, interrupt, and reorder work based on priority, but the mechanism responsible for performing that work is the Work Loop. The Work Loop acts as the execution layer of React's Fiber architecture. It processes Fiber nodes one by one, cooperates with the browser through time slicing, and ensures that rendering remains responsive even when components are deeply nested or computationally expensive. Without the Work Loop, concurrency would remain a theoretical capability rather than an actual runtime behavior.

At the core of this system is the idea that rendering is broken into discrete steps. Each Fiber represents a "unit of work," and React processes these units sequentially. After completing each unit, React inspects whether the browser's time slice has expired. This determination typically happens through a *shouldYield(*) check. If yielding is required, React immediately stops execution and resumes later from the exact same Fiber. This cooperative scheduling model is fundamental to preventing long blocking operations on the main thread.

----------------------------------------------------------------------------------------------------



## Work Loop Structure

React maintains two distinct variants of the Work Loop: a Concurrent Work Loop and a Synchronous Work Loop. The concurrent version supports yielding and is used for most non-urgent updates. Its logic resembles the following structure:

```md
function workLoopConcurrent() {
  while (workInProgress !== null && !shouldYield()) {
    workInProgress = performUnitOfWork(workInProgress);
  }
}
```md
