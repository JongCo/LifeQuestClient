<script lang="ts">
	import { goto } from "$app/navigation";
    import { onMount } from "svelte";

    type Category = {
        title: string,
        id: string,
        owner: string,
    };

    let loggedIn: boolean | null = null;

    let createCategoryTitle: string = "";

    let accessToken: string | null;

    export let categories: Array<Category> = [];

    onMount(async () => {
        accessToken = window.localStorage.getItem("accessToken");

        if(accessToken == null) {
            goto('/login');
        }

        const response = await fetch("http://localhost:8080/user/info", {
            method: "GET",
            headers: {
                Authorization: 'Bearer ' + accessToken
            }
        })

        if (response.status == 401) {
            loggedIn = false;
            window.localStorage.removeItem('accessToken');
            goto('/login');
        }
        if (response.status == 200) {
            loggedIn = true;
            console.log(await response.json());
        }
    })

    async function getCategories(){
        const response = await fetch("http://localhost:8080/category/get", {
            method: "GET",
            headers: {
                Authorization: 'Bearer ' + accessToken
            }
        });

        const responseJson = await response.json();
        console.log(responseJson);
        categories = responseJson.categories;
    }

    async function createCategory(){
        console.log(createCategoryTitle);
        const response = await fetch("http://localhost:8080/category/create", {
            method: "POST",
            headers: {
                Authorization: 'Bearer ' + accessToken,
                "Content-Type": "Application/json"
            },
            body: JSON.stringify({
                title: createCategoryTitle
            })
        });

        try{
            const responseJson = await response.json();
            console.log(responseJson);
        } catch (e){
            console.log(typeof(e));
        }

        
    }
</script>

<p>로그인에 성공하셨습니까? : {loggedIn}</p>

<button on:click={getCategories}>카테고리 가져오기</button>


<hr>
<label for="category_title">title : </label>
<input id="category_title" type="text" bind:value={createCategoryTitle}/>
<p><button on:click={createCategory}>추가하기</button></p>

<hr>
{#each categories as category}
    <p>title : {category.title} | id : {category.id} | owner : {category.owner}</p>
{/each}
