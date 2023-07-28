<!--
  - +page.svelte
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
    import {NDK} from "$lib/stores"
    import { page } from "$app/stores";
    import Post from "$lib/components/post/Post.svelte";
    import type {NDKEvent} from "@nostr-dev-kit/ndk";
    import {onMount} from "svelte";

    let event: NDKEvent | null = null;
    onMount(async () => {
        event = await $NDK.fetchEvent($page.params.id)
    })
</script>

{#if event}
    <div>
        <Post post={event} />
    </div>
{/if}