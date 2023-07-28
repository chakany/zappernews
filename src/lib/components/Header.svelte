<!--
  - Header.svelte
  - Copyright (c) 2023 Jack Chakany <jack@chaker.net>
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as published by
  - the Free Software Foundation, either version 3 of the License, or
  - (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program.  If not, see <http://www.gnu.org/licenses/>.
  -->

<script lang="ts">
    import {NDK, user} from "$lib/stores"
    import { Avatar } from "@nostr-dev-kit/ndk-svelte-components"

    $: $user?.fetchProfile()
    setTimeout(() => { user.set($user) }, 1000)
    async function login() {
        alert("lol not implemented yet just refresh with an nostr extension.")
    }
</script>

<div class="flex mx-5 my-5">
    <div class="my-auto">
        <a href="/" target="_self">Nostrich News</a>
        <select>
            <option selected>Recent 24hrs</option>
        </select>
    </div>
    <div class="flex ml-auto my-auto">
        <a class="my-auto mr-5 cursor-pointer" href="/post" target="_self">Post</a>
        <span class="my-auto">
            {#if $user}
                <Avatar class="rounded-full w-12" ndk={$NDK} npub={$user.npub} />
            {:else}
                <span class="cursor-pointer" on:click={login}>Login</span>
            {/if}
        </span>
    </div>
</div>