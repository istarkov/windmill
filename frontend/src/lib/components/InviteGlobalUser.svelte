<script lang="ts">
	import { sendUserToast } from '$lib/toast'
	import { createEventDispatcher } from 'svelte'
	import { UserService } from '$lib/gen'
	import { Button } from './common'
	import Toggle from './Toggle.svelte'
	import { generateRandomString } from '$lib/utils'
	import { globalEmailInvite } from '$lib/stores'

	const dispatch = createEventDispatcher()

	let is_super_admin = false
	let password: string = generateRandomString(10)
	let name: string | undefined
	let company: string | undefined

	async function addUser() {
		await UserService.createUserGlobally({
			requestBody: {
				email: $globalEmailInvite,
				password,
				super_admin: is_super_admin,
				name,
				company
			}
		})
		sendUserToast(`Added ${$globalEmailInvite}`)
		$globalEmailInvite = ''
		password = generateRandomString(10)
		dispatch('new')
	}
</script>

<h3 class="mb-4">Add new user to instance</h3>
<div class="flex flex-row flex-wrap gap-2 mb-2 items-end">
	<label class="block shrink min-w-0">
		<span class="text-secondary text-sm">Email</span>
		<input type="email" placeholder="email" bind:value={$globalEmailInvite} />
	</label>
	<label class="block shrink min-w-0">
		<span class="text-secondary text-sm">Password</span>
		<input bind:value={password} />
	</label>
	<div>
		<span class="text-secondary text-sm">Name (optional)</span>
		<input type="text" placeholder="name (optional)" bind:value={name} />
	</div>
	<Toggle class="mx-2" bind:checked={is_super_admin} options={{ right: 'Superadmin' }} />
	<div class="flex flex-row-reverse grow">
		<div class="flex">
			<Button
				variant="contained"
				color="dark"
				size="sm"
				on:click={addUser}
				disabled={$globalEmailInvite == '' || password == undefined}
			>
				Add user to instance
			</Button>
		</div>
	</div>
</div>
<div class="flex gap-2 items-end">
	<div class="text-xs text-tertiary grow text-right"> Email will be sent if SMTP configured </div>
</div>
