<script lang="ts">
	import { goto } from "$app/navigation";
    import { onMount } from "svelte";

    let loggedIn: boolean | null = null;
    onMount(async () => {
        const accessToken = window.localStorage.getItem("accessToken");
        
        const response = await fetch("http://localhost:8080/user/info", {
            method: "GET",
            headers: {
                Authorization: 'Bearer ' + accessToken
            }
        })

        if (response.status == 401) {
            loggedIn = false;
            goto('login');
        }
        if (response.status == 200) {
            loggedIn = true;
            console.log(await response.json());
        }
    })
</script>

<p>{loggedIn}</p>
