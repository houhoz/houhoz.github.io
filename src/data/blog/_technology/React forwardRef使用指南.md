---
title: React forwardRef 使用指南
author: Leohoo
pubDatetime: 2023-09-25T15:11:26+08:00
slug: react-forwardref-usage-guide
featured: false
draft: false
tags:
  - React
description: React forwardRef()完全指南，详解DOM元素引用、类组件实例访问和函数组件ref转发的实现方式和使用场景。
---

## Table of contents

1. Ref on a DOM element gives you a reference to the DOM Node itself:

    ```jsx
      function AutoFocusInput() {
        const inputRef = useRef(null);
        // This effect runs only once after the component mounts (like componentDidMount)
        useEffect(() => {
          // refs on regular DOM elements (e.g. the "input" tag) have access to the DOM node
          inputRef.current.focus();
        }, []);
        return <input ref={inputRef} />
      }

    ```

2. Ref on a class component gives us access to the instance, with all of it's methods and fields:

    ```jsx
      class Child extends Component {
        state = {color: "red"}
        toggleColor = () => this.setState({color: this.state.color === "red" ? "blue" : "red"})
        render() {
          return <div style={{backgroundColor: this.state.color}}>yo</div>
        }
      }

      class Parent extends Component {
        childRef = createRef();
        handleButtonClicked = () => {
          // refs on class components: hold the class component instance,
          // allowing us to call its methods!
          this.childRef.current.toggleColor();
        }
        render() {
          return (
            <div>
              <button onClick={this.handleButtonClicked}>toggle color!</button>
              <Child ref={childRef} />
            </div>
          );
        }
      }

    ```

3. Now, to finally answer your question. Refs can't be passed to function components, because They don't have instances!

    The only way to pass a ref to a function component is using forwardRef. When using forwardRef, you can simply pass the ref to a DOM element, so the parent can access it like in example 1, or you could create an object with fields and methods using the useImperativeHandle hook, which would be similar to eample 2.

    3.1 Simply passing a ref to a DOM element:

    ```jsx
      // Only when using forwardRef, the function component receives two arguments,
      // props and ref (Normally the component only gets the props argument).
      const RedInput = forwardRef((props, ref) => {
        // passing the ref to a DOM element,
        // so that the parent has a reference to the DOM node
        return <input style={{color: "red"}} {...props} ref={ref} />
      });

      function AutoFocusInput() {
        const inputRef = useRef(null);
        // This effect runs only once after the component mounts (like componentDidMount)
        useEffect(() => {
          // ref on function component is forwarded to a regular DOM element,
          // so now the parent has access to the DOM node including its focus method.
          // Note that the ref usage is the same as a regular
          // DOM element, like in example 1!
          inputRef.current.focus();
        }, []);
        return <RedInput ref={inputRef} />
      }

    ```

    3.2 Attaching the parent ref to a custom object:

    To attach functions or fields to the ref, like you could do with the instance of a class component, you need to use the `useImperativeHandle` hook:

    ```jsx
      const Child = forwardRef((props, ref) => {
        const [color, setColor] = useState("red");
        // To customize the value that the parent will get in their ref.current:
        // pass the ref object to useImperativeHandle as the first argument.
        // Then, whatever will be returned from the callback in the second argument,
        // will be the value of ref.current.
        // Here I return an object with the toggleColor method on it, for the parent to use:
        useImperativeHandle(ref, () => ({
          toggleColor: () => setColor(prevColor => prevColor === "red" ? "blue" : "red")
        }));
        return <div style={{backgroundColor: color}}>yo</div>;
      });

      class Parent extends Component {
        childRef = createRef();
        handleButtonClicked = () => {
          // Ref passed to a function component wrapped in forwardRef.
          // Note that nothing has changed for this Parent component
          // compared with the class component in example 2!
          this.childRef.current.toggleColor();
        }
        render() {
          return (
            <div>
              <button onClick={this.handleButtonClicked}>toggle color!</button>
              <Child ref={childRef} />
            </div>
          );
        }
      }

    ```
