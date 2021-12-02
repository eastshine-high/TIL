# React Router(v5 → v6)

본 페이지에서는 v5 앱에서 어떻게 빌드했을 수 있는지에 대한 코드 샘플을 보여주고 v6에서 동일한 작업을 수행하는 방법 중 자주 사용하는 부분을 정리하였습니다. 이러한 변경을 수행한 이유와 코드와 앱을 사용하는 사람들의 전반적인 사용자 경험을 모두 개선하는 방법에 대한 설명은 아래 링크를 참조하세요.

Reference : [https://reactrouter.com/docs/en/v6/upgrading/v5#introduction](https://reactrouter.com/docs/en/v6/upgrading/v5#introduction)

<hr>
<br>

### useHistory(객체) → useNavigate(함수)

v5

```jsx
import { useHistory, useParams } from "react-router-dom";

function Post() {
  const { id } = useParams();
  const history = useHistory();
  return (
    <div>
      <button
        onClick={() => {
          history.push("/");
        }}
      >
        Home
      </button>
      <button
        onClick={() => {
          history.goBack();
        }}
      >
```

[https://github.com/vlpt-playground/react-router-v5-to-v6/blob/main/src/pages/Post.js](https://github.com/vlpt-playground/react-router-v5-to-v6/blob/main/src/pages/Post.js)

v6

```jsx
import { useNavigate, useParams } from "react-router";

function Post() {
  const { id } = useParams();
  const navigate = useNavigate();
  return (
    <div>
      <button
        onClick={() => {
          navigate("/");
        }}
      >
        Home
      </button>
      <button
        onClick={() => {
          navigate(-1);
        }}
      >
```

[https://github.com/vlpt-playground/react-router-v5-to-v6/blob/v6/src/pages/Post.js](https://github.com/vlpt-playground/react-router-v5-to-v6/blob/v6/src/pages/Post.js)

<br>

### Switch → Routes

### Route의 children 또는 component → element 속성을 사용한다.

### exact Prop은 사용하지 않는다. 서브 경로가 필요한 경우 path에 *(와일드카드) 사용

- ex) `path="/users/:username/*"`
- 기본적으로 exact가 적용된다고 보면 된다.

v5

```jsx
import { Switch, Route } from "react-router-dom";

<Switch>
  <Route path="/" exact>
    <Home />
  </Route>
  <Route path="/posts/:id">
    <Post />
  </Route>
  <Route path="/users/:username">
    <User />
  </Route>
</Switch>
```

v6

```jsx
import { Routes, Route } from "react-router-dom";

<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/posts/:id" element={<Post />} />
  <Route path="/users/:username/*" element={<User />} />
</Routes>
```

<br>

### useRouteMatch → 상대경로를 설정

- `<Link to={`${match.url}/about`}>` 방식으로 설정하던 경로를 `<Link to="about">` 와 같이 상대 경로로 표현해주면 된다.
- `<Link to="/about">` 와 같이 표현한다면 절대경로이다.

### Route는 Routes의 직속 자식이어야 한다

v5

```jsx
function User() {
  const match = useRouteMatch();
  const { username } = useParams();

  return (
    <div>
      <div>
        <Link to={`${match.url}`} style={{ marginRight: 16 }}>
          @{username}
        </Link>
        <Link to={`${match.url}/about`}>About</Link>
      </div>
      <Route path={`${match.path}`} exact>
        <UserMain />
      </Route>
      <Route path={`${match.path}/about`}>
        <About />
      </Route>
    </div>
  );
}
```

v6

```jsx
function User() {
  const { username } = useParams();

  return (
    <div>
      <div>
        <Link to="" style={{ marginRight: 16 }}>
          @{username}
        </Link>
        <Link to="about">About</Link>
      </div>
      <Routes>
	      <Route path="" element={<UserMain />} />
	      <Route path="about" element={<About />} />
      </Routes>
    </div>
  );
}
```

<br>

### 서브 라우트를 구현하는 또 다른 방법 Outlet

Outlet 컴포넌트를 사용하면 서브 라우트를 조금 더 깔끔하게 정리할 수 있다.

before (App.js)

```jsx
import User from "./pages/User/User";
function App(){
	return(
		<Routes>
		  <Route path="/" element={<Home />} />
		  <Route path="/posts/:id" element={<Post />} />
		  <Route path="/users/:username/*" element={<User />} />
		</Routes>
  );
}
```

before (User.js)

```jsx
function User() {
  const { username } = useParams();

  return (
    <div>
      <div>
        <Link to="" style={{ marginRight: 16 }}>
          @{username}
        </Link>
        <Link to="about">About</Link>
      </div>
      <Routes>
	      <Route path="" element={<UserMain />} />
	      <Route path="about" element={<About />} />
      </Routes>
    </div>
  );
}
```

User 컴포넌트에 있는 서브 라우트를 Outlet 컴포넌트를 사용하여 App 컴포넌트에서 관리하도록 하자.

after (App.js)

```jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/posts/:id" element={<Post />} />
      <Route path="/users/:username/*" element={<User />}>
        <Route path="" element={<UserMain />} />
        <Route path="about" element={<About />} />
      </Route>
    </Routes>
  );
}
```

after (User.js)

```jsx
import { Link, Outlet, useParams } from "react-router-dom";

function User() {
  const { username } = useParams();

  return (
    <div>
      <div>
        <Link to="" style={{ marginRight: 16 }}>
          @{username}
        </Link>
        <Link to="about">About</Link>
      </div>
      <Outlet />
    </div>
  );
}
```
App.js에서 설정한 children들이 Outlet 컴포넌트에서 나타난다.

<br>

### Optional URL 파라미터 → 필요하면 Route 2개 만들자.

v5

```jsx
function App() {
  return (
    <Switch>
      <Route path="/" exact>
        <Home />
      </Route>
      <Route path="/optional/:value?">
        <Optional />
      </Route>
    </Switch>
  );
}
```

v6

```jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/optional/:value" element={<Optional />} />
      <Route path="/optional" element={<Optional />} />
    </Routes>
  );
}
```