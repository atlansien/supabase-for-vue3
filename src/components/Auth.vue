<template>
	<form class="row flex flex-center" @submit.prevent="handleLogin">
		<div class="col-6 form-weight">
			<h1 class="handler">
				Supabase + Vue 3
			</h1>
			<p class="description">ログイン用のマジックリンクを送信します</p>
			<div>
				<input class="inputField" type="email" placeholder="Your email" v-model="email" />
			</div>
			<div>
				<input type="submit" class="button block" :value="loading ? '送信中' : '送信済み'" :disabled="loading">
			</div>
		</div>
	</form>
</template>
<script>
import { ref } from 'vue'
import { supabase } from '../supabase'

export default {
	setup() {
		const loading = ref(false)
		const email = ref("")

    const handleLogin = async () => {
      try {
        loading.value = true
        const { error } = await supabase.auth.signIn({ email: email.value })
        if (error) throw error
        alert("メールを送信しました")
      } catch (error) {
        alert(error.error_description || error.message)
      } finally {
        loading.value = false
      }
    }


		return {
			loading,
			email,
			handleLogin
		}
	}
}

</script>
