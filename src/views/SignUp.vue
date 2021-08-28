<template>
  <div class="signup">
    <div class="hero is-info">
      <div class="hero-body has-text-centered">
        <h1 class="title">Sign up</h1>
      </div>
    </div>

    <section class="section">
        <div class="container">
            <div class="columns">
                <div class="column is-4 is-offset-4">
                    <form>
                        <div class="field">
                            <label>Email</label>
                            <div class="control">
                                <input type="email" class="input">
                            </div>
                        </div>

                        <div class="field">
                            <label>Password</label>
                            <div class="control">
                                <input type="password" class="input">
                            </div>
                        </div>

                        <div class="field">
                            <label>Repeat password</label>
                            <div class="control">
                                <input type="password" class="input">
                            </div>
                        </div>

                        <div class="field">
                            <div class="control">
                                <button class="button is-dark">Sign up</button>
                            </div>
                        </div>
                    </form>

                    <hr>

                    Or <router-link to="/log-in">click here</router-link> to log in!
                </div>
            </div>
        </div>
    </section>
  </div>
</template>
