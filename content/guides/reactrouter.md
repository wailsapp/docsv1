+++
title = "Using React Router"
date = 2019-12-25T04:56:50+10:00
weight = 20
chapter = false
+++

It's possible to use React Router, but you must ensure that you don't use `<BrowserRouter>` and use one of these options :  
- `<MemoryRouter>` [Documentation](https://reactrouter.com/web/api/MemoryRouter)  
- `<HashRouter>` [Documentation](https://reactrouter.com/web/api/HashRouter)  

```jsx
/* Example */
<MemoryRouter> // or: <HashRouter>
    <div>
        <ul>
            <li>
                <Link to="/">Home</Link>
            </li>
            <li>
                <Link to="/about">About</Link>
            </li>
        </ul>
        <hr/>
        <Switch>
            <Route exact path="/">
                <Home />
            </Route>
            <Route path="/about">
                <About />
            </Route>
        </Switch>
    </div>
</MemoryRouter> // or: </HashRouter>
```
