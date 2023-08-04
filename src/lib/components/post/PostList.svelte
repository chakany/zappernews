<!--
  - PostList.svelte
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
    import Post from "$lib/components/post/Post.svelte"
    import type {NDKEvent} from "@nostr-dev-kit/ndk";

    export let posts: NDKEvent[];
</script>

{#if posts.length == 0}
    No Content, go post something!
{/if}

{#each posts as post, index}
    <Post index={index} post={post} />
{/each}