## 자바스크립트 Extend 설명

```js
"use client";
import { useForm } from "react-hook-form";
import PocketBase from "pocketbase";
import { useState } from "react";
const pb = new PocketBase("http://127.0.0.1:8090");

export default function Loginpage() {
  const [isLoading, setLoading] = useState(false);
  const [dummy, setDummy] = useState(0); // dummy state to force re-render
  const { register, handleSubmit } = useForm();

  const isLoggedIn = pb.authStore.isValid;
  async function login(data: any) {
    setLoading(true);
    try {
      const authData = await pb
        .collection("users")
        .authWithPassword(data.username, data.password);
    } catch (e) {
      console.log(e);
    }
    setLoading(false);
  }
  function logout() {
    pb.authStore.clear();
    setDummy(Math.random());
  }
  if (isLoggedIn) {
    return (
      <>
        <h1>Logged In: {pb.authStore.token}</h1>
        <button onClick={logout}>Log out</button>
      </>
    );
  }
  return (
    <>
      {isLoading && <h1>Loading...</h1>}
      <h1>Logged In: {isLoggedIn && pb.authStore.token}</h1>
      <form onSubmit={handleSubmit(login)}>
        <input type="text" placeholder="username" />
        <input
          type="password"
          placeholder="password"
          {...register("password")}
        />
        <button type="submit">{isLoading ? "Login" : "Loading"}</button>
      </form>
    </>
  );
}
```

1. 임포트 및 설정:

- use client: Next.js 애플리케이션에서 코드가 클라이언트 측(브라우저)에서만 실행되어야 한다는 것을 나타냅니다.

- react-hook-form의 useForm: 폼 상태 및 유효성 검사를 관리하는 훅을 가져옵니다.

- PocketBase: PocketBase 백엔드 서버와 상호 작용하는 라이브러리를 가져옵니다.

- react의 useState: 컴포넌트 상태를 관리하는 useState 훅을 가져옵니다.

- pb 상수는 PocketBase를 사용하여 만들어지며, 일반적으로 http://127.0.0.1:8090의 로컬 인스턴스를 가리킵니다.

2. 컴포넌트 상태:

- isLoading: 로그인 프로세스가 진행 중인지 추적하는 상태 변수(부울).
- dummy: 임시 해결 방법으로, UI 업데이트를 처리하기 위해 재렌더링을 강제하는 상태 변수(숫자).

3. 폼 처리:

- useForm 훅은 폼 상태 및 유효성 검사를 관리하는 register 및 handleSubmit 함수를 제공합니다.
- register는 유효성 검사를 위해 폼 필드를 등록하는 데 사용됩니다(여기서는 비밀번호 필드만 표시됨).
- handleSubmit는 폼 제출을 처리하고 login 함수를 트리거합니다.

4. 로그인 상태 확인:

- isLoggedIn: pb.authStore.isValid를 사용하여 유효한 사용자 세션이 있는지 확인합니다.

5. 로그인 함수 (login):

- 폼 데이터 (data)를 입력으로 받으며, 사용자 이름과 비밀번호를 포함합니다.로그인 진행률을 나타내기 위해 isLoading을 true로 설정합니다.pb.collection('users').authWithPasswor(data.username, data.password)을 사용하여 PocketBase로 사용자 인증을 시도합니다. 인증 중에 발생하는 모든 오류를 catch하여 콘솔에 기록합니다. 로그인 시도(성공 또는 실패) 후 isLoading을 다시 false로 설정합니다.

6. 로그아웃 함수 (logout):

- pb.authStore.clear()를 사용하여 사용자 세션을 지웁니다. setDummy(Math.random())을 사용하여 재렌더링을 트리거합니다.

7. 조건부 렌더링:

- isLoggedIn이 true인 경우 현재 사용자 토큰과 로그아웃 버튼이 있는 메시지를 표시합니다 그렇지 않으면 로그인 폼을 표시합니다. isLoading이 true인 경우 로딩 메시지를 표시합니다 isLoggedIn에 따라 현재 사용자 토큰을 조건부로 표시합니다.사용자 이름과 비밀번호 필드가 있는 폼을 렌더링합니다.비밀번호 필드는 react-hook-form에 등록됩니다. 제출 버튼 텍스트는 isLoading에 따라 "Login" 또는 "Loading"으로 변경됩니다. 전반적으로 이 코드 조각은 다음을 수행하는 로그인 페이지를 생성합니다.

- 기존 사용자 세션을 확인합니다.사용자 이름과 비밀번호를 사용하여 사용자 로그인을 허용합니다.로딩 상태 및 잠재적인 오류를 처리하여 로그인 프로세스를 처리합니다. 인증된 사용자를 위한 로그아웃 버튼을 제공합니다.
