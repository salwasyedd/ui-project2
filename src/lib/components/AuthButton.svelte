<script lang="ts">
  // demo user for sign in prototype
  const demoUser = { name: 'Test Man', email: 'test_man102@yahoo.com' };

  const initials = demoUser.name
    .split(' ')
    .map((word) => word[0])
    .join('')
    .slice(0, 2)
    .toUpperCase();

  let showModal = $state(false);
  let isSignedIn = $state(false);
  let username = $state('');
  let staySignedIn = $state(true);
  let error = $state('');

  const open = () => (showModal = true);
  const close = () => {
    showModal = false;
    error = '';
  };


  function handleNext(e: SubmitEvent) {
    e.preventDefault();

    // any text typed in = invalid login
    if (username.trim() !== '') {
      error = 'Invalid: Username, email or phone number';
      return;
    }

    // empty field = prototype sign-in
    error = '';
    isSignedIn = true;
    close();
  }

  function signOut() {
    isSignedIn = false;
    close();
  }


  $effect(() => {
    document.body.style.overflow = showModal ? 'hidden' : '';
    return () => (document.body.style.overflow = '');
  });
</script>


{#if isSignedIn}
  <button type="button" class="profile-avatar" aria-label="Account menu" onclick={open}>
    {initials}
  </button>
{:else}
  <button type="button" class="signin-button" onclick={open}>Sign In</button>
{/if}

{#if showModal}
  <div class="modal-overlay" role="presentation">
    {#if isSignedIn}
      <!-- SIGNED-IN: account card -->
      <div class="auth-modal account" role="dialog" aria-modal="true" aria-label="Account">
        <button type="button" class="auth-close" aria-label="Close" onclick={close}>✕</button>

        <div class="account-header">
          <span class="account-avatar">{initials}</span>
          <div>
            <div class="account-name">{demoUser.name}</div>
          </div>
        </div>

        <div class="account-actions">
          <button type="button" class="account-btn" onclick={signOut}>
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor"
              stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4" />
              <path d="M16 17l5-5-5-5" />
              <path d="M21 12H9" />
            </svg>
            Sign out
          </button>
        </div>

        <div class="account-footer">
          <button type="button" class="link-button">Account settings</button>
          <button type="button" class="link-button">Help</button>
        </div>
      </div>
    {:else}
      <!-- SIGNED-OUT: sign-in card -->
      <div class="auth-modal" role="dialog" aria-modal="true" aria-labelledby="auth-title">
        <button type="button" class="auth-close" aria-label="Close" onclick={close}>✕</button>

        <h2 id="auth-title">Sign In</h2>

        <form onsubmit={handleNext}>
          <label class="auth-label" for="auth-username">Username, email or phone number</label>
          <input
  id="auth-username"
  type="text"
  bind:value={username}
  class:invalid={error}
  aria-invalid={error ? 'true' : 'false'}
  aria-describedby={error ? 'auth-error' : undefined}
  oninput={() => (error = '')}
/>
{#if error}
  <p id="auth-error" class="auth-error" role="alert">{error}</p>
{/if}

          <div class="auth-row">
            <label class="auth-check">
              <input type="checkbox" bind:checked={staySignedIn} />
              Stay signed in
            </label>
            <button type="button" class="link-button">Forgot username</button>
          </div>

          <button type="submit" class="auth-primary">Next</button>
        </form>

        <div class="auth-divider"><span>or</span></div>

        <button type="button" class="auth-google">
          Sign in with Google
        </button>

        <button type="button" class="auth-create">Create account</button>
      </div>
    {/if}
  </div>
{/if}

<style>
  /* header buttons */
  .signin-button {
    background: none;
    border: none;
    padding: 0;
    color: #6001d2;
    font-size: 14px;
    font-weight: 600;
  }

  /* .profile-avatar's circle look comes from app.css; this just resets the button */
  .profile-avatar {
    border: none;
    font-size: 14px;
    font-weight: 700;
  }

  /* shared card */
  .auth-modal {
    position: relative;
    width: 100%;
    max-width: 380px;
    padding: 40px 32px 36px;
    background: #000;
    color: #fff;
    border: 1px solid #2c2c2c;
    border-radius: 16px;
  }

  .auth-modal h2 {
    margin: 0 0 28px;
    font-size: 24px;
    font-weight: 700;
    color: #fff;
  }

  .auth-label {
    display: block;
    font-size: 13px;
    font-weight: 600;
    color: #b4b4bc;
  }

  .auth-modal input[type='text'] {
    display: block;
    width: 100%;
    height: 52px;
    margin: 10px 0 16px;
    padding: 0 14px;
    background: transparent;
    color: #fff;
    border: 1px solid #6b6b75;
    border-radius: 4px;
  }

  .auth-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 26px;
    font-size: 14px;
  }

  .auth-check {
    display: flex;
    align-items: center;
    gap: 10px;
    color: #b4b4bc;
  }

  .auth-check input {
    width: 18px;
    height: 18px;
    accent-color: #8e7cf6;
  }

  .link-button {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: none;
    border: none;
    padding: 0;
    font-size: 14px;
    color: #b4b4bc;
    text-decoration: underline;
  }

  .auth-primary,
  .auth-google {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
    width: 100%;
    height: 52px;
    border-radius: 999px;
    font-size: 16px;
    font-weight: 700;
  }

  .auth-primary {
    background: #8e7cf6;
    color: #000;
    border: none;
  }
  .auth-primary:hover { background: #a090f8; }

  .auth-google {
    background: transparent;
    color: #fff;
    border: 1px solid #2f2f2f;
  }
  .auth-google:hover { background: #111; }

  .auth-divider {
    display: flex;
    align-items: center;
    gap: 14px;
    margin: 24px 0;
    font-size: 13px;
    color: #b4b4bc;
  }
  .auth-divider::before,
  .auth-divider::after {
    content: '';
    flex: 1;
    height: 1px;
    background: #2f2f2f;
  }

  .auth-create {
    display: block;
    width: 100%;
    margin-top: 32px;
    background: none;
    border: none;
    color: #8e7cf6;
    font-size: 15px;
    font-weight: 700;
    text-align: center;
  }

  .auth-close {
    position: absolute;
    top: 14px;
    right: 14px;
    width: 32px;
    height: 32px;
    background: transparent;
    border: none;
    border-radius: 50%;
    color: #8a8a94;
  }
  .auth-close:hover { color: #fff; }

  /* signed-in account card */
  .auth-modal.account {
    max-width: 360px;
    padding: 28px 24px 22px;
  }

  .account-header {
    display: flex;
    align-items: center;
    gap: 14px;
    margin-bottom: 22px;
    padding-right: 28px;
  }

  .account-avatar {
    flex-shrink: 0;
    width: 56px;
    height: 56px;
    display: grid;
    place-items: center;
    border: 2px solid #8e7cf6;
    border-radius: 50%;
    font-size: 18px;
    font-weight: 700;
    color: #fff;
  }

  .account-name {
    font-size: 20px;
    font-weight: 700;
  }

  .account-actions {
    display: flex;
    gap: 10px;
  }

  .account-btn {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    height: 48px;
    padding: 0 8px;
    background: transparent;
    color: #fff;
    border: 1px solid #6b6b75;
    border-radius: 999px;
    font-size: 14px;
    font-weight: 700;
    white-space: nowrap;
  }
  .account-btn:hover { background: #111; }

  .account-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 22px;
    padding-top: 16px;
    border-top: 1px solid #2f2f2f;
  }

  .auth-modal input[type='text'].invalid {
  border-color: #ff6b6b;
}

.auth-error {
  margin: -6px 0 16px;
  font-size: 13px;
  color: #ff6b6b;
}
</style>