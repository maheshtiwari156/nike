<script type="text/javascript" data-app-id="APP-ID-CODE" src="https://assets.yammer.com/assets/platform_js_sdk.js"></script>


<div id='page'>
	<div>
		<h2>JS SDK</h2>
		<button id="yammer-js-login-button">JS Login</button>
		<button id="yammer-user-button">Get Current User</button>
		<button id="yammer-group-button">Get Current Groups</button>
	</div>

	<div class="logged-in" style="display: none">
		<p>User is now signed in to the app using Yammer</p>
		<button id="disconnect" class="yj-btn yj-btn-alt">Log out from your Yammer account</button>
	</div>
	<div class="logged-in" style="display: none">
		<h2>Authentication Logs</h2>
		<pre id="authResult"></pre>
	</div>

	<div style="clear: both">&nbsp;</div>
	<div>
		<button id="get-messages">Get messages</button>
		<div class="messages-in" style="display: none">
			<h2>Messages</h2>
			<pre id="messages"></pre>
		</div>
	</div>

	<div style="clear: both">&nbsp;</div>

	<div>
		<input type="text" id="message" />
		<button id="send-message">Send message</button>
	</div>

</div>
