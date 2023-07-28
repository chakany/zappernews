<!--
  - +layout.svelte
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
    import "../app.css";
    import Header from "$lib/components/Header.svelte"
	import Footer from "$lib/components/Footer.svelte"
	import { NDKNip07Signer, NDKUser } from "@nostr-dev-kit/ndk";
    import {NDK, user} from "$lib/stores";
    import {onMount} from "svelte";

    onMount(async () => {
		const pubkey = localStorage.getItem("pubkey")
		if (pubkey) {
			try {
				const signer = new NDKNip07Signer();
				$NDK.signer = signer
			} catch (e) {
				console.error(e)
				alert("error with signer; check that you have one installed or enabled")
			}
			$user = new NDKUser({ npub: pubkey })
			$user.ndk = $NDK
		}
        $NDK.connect()
    })
</script>

<Header />
<div class="container">
	<slot />
</div>

<Footer />