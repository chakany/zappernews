<!--
  - Post.svelte
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
    import {NDK} from "$lib/stores";
    import {NDKEvent} from "@nostr-dev-kit/ndk";
    import {onMount} from "svelte";
    import {Name} from "@nostr-dev-kit/ndk-svelte-components";

    export let post: NDKEvent;
    export let index: number = -1;

    let comments: NDKEvent[] = []
    let noOfUpvotes = 0;
    onMount(async () => {
        comments = Array.from(await $NDK.fetchEvents({
            kinds: [1],
            "#e": [post.id]
        }))
        // magically this works even though it's an error so
        comments.sort((a, b) => a.created_at > b.created_at)
        // TODO: make this also just an array
        const upvotes = await $NDK.fetchEvents({
            kinds: [7],
            "#e": [post.id]
        })

        noOfUpvotes = upvotes.size
    })

    async function upvote() {
        try {
            await $NDK.assertSigner()
        } catch (error) {
            alert("you need to login.")
            return
        }

        const ev = new NDKEvent();
        ev.ndk = $NDK;
        ev.kind = 7;
        ev.content = `+`;
        ev.tags = [
            ["e", post.id]
        ]

        await ev.publish()
        noOfUpvotes++ // maybe this should only happen if even successfuly published.
    }

    let commentText = ""
    async function postComment() {
        if (commentText == "") {
            alert("must write a comment first")
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
        ev.kind = 1;
        ev.content = commentText;
        ev.tags = [
            ["e", post.id]
        ]

        await ev.publish()
    }
</script>

<div class="flex my-auto gap-3">
    {#if index > -1}
        <span class="my-auto">{index + 1}</span>
    {/if}
    <span on:click={upvote} class="my-auto cursor-pointer">^</span>
    <div class="flex flex-col my-auto">
        <div class="flex">
            <div class="flex gap-2">
                <a class="cursor-pointer" href={post.getMatchingTags("url")[0][1]}>{post.getMatchingTags("title")[0][1]}</a>
                <a class="cursor-pointer" href={"/?site=" + post.getMatchingTags("r")[0][1] + ""} target="_self">({post.getMatchingTags("r")[0][1]})</a>
            </div>
        </div>
        {#if index === -1}
            <p>{post.content.split(post.getMatchingTags("url")[0][1])[1]}</p>
        {/if}
        <small class="flex gap-3">
            <span>{noOfUpvotes} upvotes</span>
            <a href={"/post/" + post.encode()} target="_self">{comments.length} comments</a>
            <a href="https://nosta.me/{post.author.npub}">by <Name ndk={$NDK} user={post.author} /></a>
        </small>
    </div>
</div>

{#if index === -1}
    <div>
        Comments:
        <div class="flex flex-col">
            Write a comment:
            <textarea class="border-black border-2" bind:value={commentText}></textarea>
            <span class="cursor-pointer" on:click={postComment}>Post Comment</span>
        </div>
        <div>
            {#each comments as comment, i}
                <div class="flex flex-col border-2 border-black">
                    <p>{comment.content}</p>
                    <div class="flex gap-3">
                        <span>{new Date(comment.created_at * 1000).toLocaleString()}</span>
                        <span>reply soon</span>
                        <a href="https://nosta.me/{comment.author.npub}">by <Name ndk={$NDK} user={comment.author} /></a>
                    </div>
                </div>
            {/each}
        </div>
    </div>
{/if}