<script lang="ts">
	import { page } from '$app/stores'	
	import { userData } from '$lib/store/userStore'
	import { logOutUser } from '$lib/apis/auth.api'
</script>

<header>

	<nav class="float-right">
		<svg viewBox="0 0 2 3" aria-hidden="true">
			<path d="M0,0 L1,2 C1.5,3 1.5,3 2,3 L2,0 Z" />
		</svg>
		<ul>
			<li class:active={$page.url.pathname === '/'}>
				<a sveltekit:prefetch href="/">Home</a>
			</li>
			{#if !$userData.username}
				<li class:active={$page.url.pathname === '/accounts/login'}>
					<a sveltekit:prefetch href="/accounts/login">Login</a>
				</li>
				<li class:active={$page.url.pathname === '/accounts/register'}>
					<a sveltekit:prefetch href="/accounts/register">Register</a>
				</li>
			{:else}
				<li>
					Hi, <a sveltekit:prefetch href="/accounts/user/{$userData.username}-{$userData.id}"
						>{$userData.username}</a
					>
				</li>
				<li>
					<a href={null} on:click={logOutUser} style="cursor: pointer">Logout</a>
				</li>
			{/if}
		</ul>
		<svg viewBox="0 0 2 3" aria-hidden="true">
			<path d="M0,0 L0,3 C0.5,3 0.5,3 1,2 L2,0 Z" />
		</svg>
	</nav>

</header>
