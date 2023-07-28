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
    import {NDKEvent} from "@nostr-dev-kit/ndk";

    let title = ""
    let url = ""
    let description = ""

    async function post() {
        if (title == "") {
            alert("Please input a title")
            return
        }
        try {
            await $NDK.assertSigner()
        } catch (error) {
            alert("you need to login.")
            return
        }

        const ev = new NDKEvent();
        ev.ndk = $NDK;
        ev.kind = 2000;
        ev.content = `${title ? title + "\n" : ""}${url}${description ? "\n\n" + description : ""}`;
        const filterDomain = (domain) => {
            domain = domain.split('.');
            domain.reverse();
            return `${domain[1]}.${domain[0]}`;
        }

        ev.tags = [
            ["url", url],
            ["title", title],
            ["r", filterDomain(new URL(url).hostname)]
        ]

        await ev.publish()
    }
</script>

Make a post

<div class="flex flex-col gap-3">
    <div>
        Title *
        <input class="border-black border-2" type="text" bind:value={title} />
    </div>
    <div>
        URL
        <input class="border-black border-2" type="url" bind:value={url} />
    </div>
    <div>
        Content
        <textarea class="border-black border-2" type="text" bind:value={description}></textarea>
    </div>
    <span class="cursor-pointer" on:click={post}>Post</span>
</div>