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
    import { NDK } from "$lib/stores"
    import { NDKEvent } from "@nostr-dev-kit/ndk";
    import {onMount} from "svelte";
    import { page } from "$app/stores";
    import PostList from "$lib/components/post/PostList.svelte";

    onMount(async () => {
        const filterDomain = $page.url.searchParams.get("site");
        const filter = {
            kinds: [2000],
            "since": Math.floor(new Date(new Date().getTime() - (24 * 60 * 60 * 1000)).getTime() / 1000)
        }
        if (filterDomain) filter["#r"] = [filterDomain];
        posts = Array.from(await $NDK.fetchEvents(filter))
        const postIds = []
        for (let i = 0; i < posts.length; i++) {
            postIds.push(posts[i].id)
        }
        console.log(postIds)
        upvotes = Array.from(await $NDK.fetchEvents({
            kinds: [7],
            "#e": postIds
        }))
        posts = posts
    })

    let posts: NDKEvent[] = []
    let upvotes: NDKEvent[] = []

    $: posts.sort((a, b) => {
        const aUpvotes = upvotes.filter((upvote) => upvote.getMatchingTags("e")[0][1] === a.id).length
        const bUpvotes = upvotes.filter((upvote) => upvote.getMatchingTags("e")[0][1] === b.id).length
        return bUpvotes - aUpvotes
    })
</script>

<div class="flex flex-col">
    <PostList posts={posts} />
</div>