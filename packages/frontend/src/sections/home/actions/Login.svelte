<script>
  import { Animate, Button } from '/components/index.js'
  import { Member }          from '/lib/index.js'
  import { screen }          from '/stores/screen.js'

  let email      = ''
  let loading    = false
  let message    = ''
  let reLoggedIn = false

  const reLogin = async () => {
    try {
      await Member.login()
      if (await Member.isMember()) {
        screen.set('dashboard')
      } else {
        Member.logout()
      }
    } catch (e) {
      console.log(e.message)
    }

    reLoggedIn = true
  }

  const login = async email => {
    loading = true
    message = 'Check your inbox [including your spam folder].'

    try {
      console.log('gonna login')
      await Member.login()
      console.log('loggedin')
      if (await Member.isMember()) {
        screen.set('dashboard')
      } else {
        Member.logout()
        message = 'Your account needs to be activated by an admin. RHcoop will send you an email as soon as your account is ready for log in. Keep an eye on your inbox [and spam folder too].'
      }
    } catch (e) {
      message = e.message
    }

    loading = false
  }

  reLogin()
</script>

<Animate>
  <section class="flex column">
    {#if reLoggedIn}
      <div class="flex">
        <input class="space-right" bind:value={email} placeholder="name@email.com" />
        <Button disabled={loading} on:click={() => login(email)}>login</Button>
      </div>
      <p class="info space-top">{message}</p>
    {:else}
      <p class="small">checking if a member is already logged in ...</p>
    {/if}
  </section>
</Animate>
