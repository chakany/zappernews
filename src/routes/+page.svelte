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
    const ev = new NDKEvent()

    onMount(() => {
        ev.ndk = $NDK
    })

    let posts: NDKEvent[] = []

    async function fetchPosts() {
        console.log(Math.floor(new Date(new Date().getTime() - (24 * 60 * 60 * 1000)).getTime() / 1000))
        const filterDomain = $page.url.searchParams.get("site");
        const filter = {
            kinds: [2000],
            "since": Math.floor(new Date(new Date().getTime() - (24 * 60 * 60 * 1000)).getTime() / 1000)
        }
        if (filterDomain) filter["#r"] = [filterDomain];
        posts = Array.from(await $NDK.fetchEvents(filter))

        console.log(posts)
    }
    fetchPosts();

    async function post(title: string, url: string, content: string) {
        ev.content = content // preserve readability in other clients
        ev.tags = [
            ["r", new URL(url).host], // should be the domain name including subdomains
            ["title", title],
            ["url", "https://google.com"]
        ]
        ev.kind = 2000;

        await ev.publish()
    }
    //setTimeout(() => post("hello title", "https://google.com", "test test test"), 3000)
</script>

<div class="flex flex-col">
    <PostList posts={posts} />
</div>