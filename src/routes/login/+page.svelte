<script lang="ts">
	import { goto } from "$app/navigation";
	import { onMount } from "svelte";


    type LifeQuestLoginResponse = {
        grantType: string,
        accessToken: string,
        refreshToken: string
    }

    onMount(async () => {
        const accessToken = window.localStorage.getItem("accessToken");

        if (accessToken != null){
            goto("/service");
        }
    })

    // 로그인 요청시 보낼 값
    // DOM과 연동함
    let id:string = "";
    let password:string = "";

    async function fetchLogin(): Promise<LifeQuestLoginResponse> {
        const response = await fetch("http://localhost:8080/user/login", {
            method: "POST",
            body: JSON.stringify({
                username: id,
                password: password
            }),
            headers: {
                "Content-Type": "application/json"
            }
        })
        
        const responseJson: LifeQuestLoginResponse = await response.json();



        if(responseJson.grantType == undefined) 
            return Promise.reject(new Error('Not contains \'grantType\''));
        if(responseJson.accessToken == undefined) 
            return Promise.reject(new Error('Not contains \'accessToken\''));
        if(responseJson.refreshToken == undefined) 
            return Promise.reject(new Error('Not contains \'refreshToken\''));

        const result: LifeQuestLoginResponse = {
            grantType: responseJson.grantType,
            accessToken: responseJson.accessToken,
            refreshToken: responseJson.refreshToken
        };

        return result;
    }

    async function submit(): Promise<void>{
        let loginSuccess = false;
        try {
            const response = await fetchLogin();
            // 문자열로 하드코딩하는거 HJ Guru님께 여쭈어보자
            window.localStorage.setItem("accessToken", response.accessToken);
            window.localStorage.setItem("refreshToken", response.refreshToken);

            loginSuccess = true;
        } catch (error) {
            console.error(error);
        }

        if(loginSuccess) goto('/service');
    }

</script>

<h1>메인페이지입니다.</h1>
<h3>로그인을 해봅시다.</h3>
<p>ID</p>
<input bind:value={id} />
<br/>
<p>Password</p>
<input type="password" bind:value={password} />
<br/>
<p>현재 입력중인 ID : {id}</p>
<p>현재 입력중인 Password : {password}</p>

<button on:click={submit}>로그인 ㄱㄱ</button>