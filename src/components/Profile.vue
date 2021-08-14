<template>
	<form class="form-widget" @submit-prevent="updateProfile">
		<div>
			<label for="email">Email</label>
			<input id="email" type="text" :value="store.user.email" disabled>
		</div>
		<div>
			<label for="username">名前</label>
			<input type="text" id="username" v-model="username">
		</div>
		<div>
			<label for="website">登録ウェブサイト</label>
			<input type="website" id="website" v-model="website">
		</div>

		<div>
			<input type="submit" class="button block primary" :value="loading ? 'loading': 'update'" :disabled="loading">
		</div>
		<div>
			<button class="button block" @click="signOut" :disabled="loading">
				ログアウト
			</button>
		</div>
	</form>
</template>

<script>
import { supabase } from '../supabase'
import { store } from '../store'
import { onMounted, ref } from 'vue'

export default {
	setup() {
		const loading = ref(true)
		const username = ref("")
		const website = ref("")
		const avator_url = ref("")

		const getProfile = async () => {
			try {
				loading.value = true
				store.user = supabase.auth.user()

				let { data, e, status } = await supabase
					.from("profiles")
					.select("username website avator_url")
					.eq("id", store.user.id)
					.single()

				if (e && status !== 406) throw e

				if(!data) return
				username.value = data.username
				website.value = data.website
				avator_url.value = data.avator_url
			} catch(e) {
				alert(e.message)
			} finally {
				loading.value = false
			}
		}

		const updateProfile = async () => {
			try {
				loading.value = true
				store.user = supabase.auth.user()

				const updates = {
					id: store.user.id,
					username: username.value,
					website: website.value,
					avator_url: avator_url.value,
					updated_at: new Date()
				}

				let { e } = await supabase
					.from("profiles")
					.upsert(updates, { returning: "minimal" })
				
				if (e) throw e
			} catch(e) {
				alert(e.message)
			} finally {
				loading.value = false
			}
		}

		const signOut = async () => {
			try {
				loading.value = true
				let { e } = await supabase.auth.signOut()
				if (e) throw e
			} catch(e) {
				alert(e.message)
			} finally {
				loading.value = false
			}
		}

		onMounted(() => {
			getProfile()
		})

		return {
			store,
			loading,
			username,
			website,
			avator_url,
			updateProfile,
			signOut
		}
	}
}
</script>
