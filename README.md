# article-approval-form

Here’s a step-by-step guide for setting up Auth0 authentication on your static HTML pages (like index.html, approve-change.html, etc.) using the Auth0 SPA JS SDK and your Netlify environment:

1. Add the Auth0 SPA JS SDK to Your HTML
Add this line to your <head> or just before your closing </body> tag:

xml
<script src="https://cdn.auth0.com/js/auth0-spa-js/2.0/auth0-spa-js.production.js"></script>

2. Get Your Auth0 Application Credentials
Go to your Auth0 Dashboard.

Create a new Single Page Application (SPA) if you haven’t already.

Copy your Domain and Client ID from the application settings.

3. Add Your Credentials to Your HTML or as Environment Variables
For a static HTML site, you can add them directly in a <script> block (for production, use Netlify’s environment variables and inject them via your build process or use a serverless function):

xml
<script>
  window.AUTH0_DOMAIN = 'YOUR_DOMAIN.auth0.com';
  window.AUTH0_CLIENT_ID = 'YOUR_CLIENT_ID';
</script>

4. Initialize Auth0 in Your JavaScript
Add this JavaScript to your page (after the SDK and your variables):

xml
<script>
let auth0 = null;

async function configureClient() {
  auth0 = await createAuth0Client({
    domain: window.AUTH0_DOMAIN,
    client_id: window.AUTH0_CLIENT_ID,
    // cacheLocation: 'memory' (default), or 'localstorage' for dev/testing [4]
  });
}

async function login() {
  await auth0.loginWithRedirect({
    redirect_uri: window.location.origin
  });
}

async function logout() {
  auth0.logout({
    returnTo: window.location.origin
  });
}

async function requireAuth() {
  await configureClient();
  const isAuthenticated = await auth0.isAuthenticated();
  if (!isAuthenticated) {
    await login();
  }
  // else: show your page content
}

requireAuth();
</script>

5. Secure Your Pages
Place the above logic in every page you want protected.

The user will be redirected to Auth0 to log in if not already authenticated.

After login, they’ll be redirected back to your page.

6. (Optional) Use Netlify Environment Variables
If you want to avoid hardcoding secrets, you can use Netlify’s environment variables:

In Netlify UI: Site settings > Environment variables

Add AUTH0_DOMAIN and AUTH0_CLIENT_ID.

In your JS, reference them using Netlify’s Injected Variables:

javascript
window.AUTH0_DOMAIN = process.env.AUTH0_DOMAIN;
window.AUTH0_CLIENT_ID = process.env.AUTH0_CLIENT_ID;
Note: For pure static HTML, you may need a build step to inject these, or use a small Netlify Function to serve them securely.

7. Add Login/Logout Buttons (Optional)
xml
<button onclick="login()">Login</button>
<button onclick="logout()">Logout</button>
Summary Table
Step What to Do
1. Add SDK <script src="https://cdn.auth0.com/js/auth0-spa-js/2.0/auth0-spa-js.production.js"></script>
2. Get Credentials From Auth0 Dashboard: Domain, Client ID
3. Add to HTML Set window.AUTH0_DOMAIN and window.AUTH0_CLIENT_ID
4. Init Auth0 Use createAuth0Client({...}) in JS
5. Require Auth Call requireAuth() on page load
6. (Optional) Env Use Netlify env vars for secrets
7. (Optional) UI Add login/logout buttons
You can now protect any HTML page with Auth0 authentication on Netlify!

If you want a full working HTML example, just ask!
