<script lang="ts">
    function clearLocalStorage(){
        window.localStorage.clear();
    }
</script>

<h1>Welcome to SvelteKit</h1>
<p>그냥 메인페이지 입니다.</p>
<p>로그인 하는것으로 시작합니다.</p>
<p>로그인하려면 <a href="/login">로그인</a> 페이지로 가시요</p>

<p>만약 localStorage를 날리고 싶다면 <button on:click={clearLocalStorage}>이 버튼</button>를 클릭해보세요</p>