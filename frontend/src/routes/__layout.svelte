<script lang="ts">
	import { userData } from '$lib/store/userStore'
	import { navigating } from '$app/stores'
	import { loading } from '$lib/store/loadingStore'
	import { notificationData } from '$lib/store/notificationStore'
	import { fly } from 'svelte/transition'
	import { afterUpdate, onMount } from 'svelte'

	import Header from '../components/Header/Header.svelte'
	import Loader from '../components/Loader/Loader.svelte'

	import '../dist/css/style.min.css'

	$: loading.setNavigate(!!$navigating)
	$: loading.setLoading(!!$navigating, 'Loading, please wait...')

	import * as StorageService from '$lib/services/localstorage.service'
	import { getCurrentUser } from '$lib/apis/auth.api'
	import { variables } from '$lib/utils/constants'

	onMount(async () => {
		if (StorageService.getStorage('refreshToken')) {
			const [response, errs] = await getCurrentUser(
				fetch,
				`${variables.BASE_API_URI}/token/refresh/`,
				`${variables.BASE_API_URI}/user/`
			)
			if (errs.length <= 0) {
				userData.set(response)
			}
		}
	})

	afterUpdate(async () => {
		const notifyEl = document.getElementById('notification') as HTMLElement
		// const notifyEl = document.getElementsByClassName('notification')
		if (notifyEl && $notificationData !== '') {
			setTimeout(() => {
				notifyEl.classList.add('disappear')
				notificationData.set('')
			}, 3000)
		}
		if (StorageService.getStorage('refreshToken')) {
			const [response, _] = await getCurrentUser(
				fetch,
				`${variables.BASE_API_URI}/token/refresh/`,
				`${variables.BASE_API_URI}/user/`
			)
			userData.update(() => response)
		}
	})
</script>

<Header />

{#if $notificationData}
	<p
		class="notification"
		id="notification"
		in:fly={{ x: 200, duration: 500, delay: 500 }}
		out:fly={{ x: 200, duration: 500 }}
	>
		{$notificationData}
	</p>
{/if}

<main>
	<Loader />
	<slot />
</main>

<footer in:fly={{ y: -50, duration: 500, delay: 500 }} out:fly={{ duration: 500 }}>
	<p>
		Django + Sveltekit Authorization
	</p>
</footer>
