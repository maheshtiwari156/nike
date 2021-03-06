<!DOCTYPE html>
<html>

<head>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
    <script type="text/javascript" data-app-id="48076210176-6cy124u8Ia0OTOTt8ahCXgE" src="https://assets.yammer.com/assets/platform_js_sdk.js"></script>
</head>

<body>
    <script>
        yam.connect.embedFeed({
            container: '#embedded-feed',
            feedType: 'open-graph',
            feedId: '',
            config: {
                use_sso: false,
                header: true,
                footer: true,
                showOpenGraphPreview: false

            },
            objectProperties: {
                url: '',
                type: 'page'
            }
        });

        yam.connect.actionButton({
            container: "#embedded-follow",
            action: "follow"
        });

        yam.connect.actionButton({
            container: "#embedded-like",
            action: "like"
        });
        yam.config({
            debug: true
        });

        function logout() {
            yam.platform.getLoginStatus(
                function (response) {
                    if (response.authResponse) {
                        yam.platform.logout(function (response) {
                            toggleLoginStatus(false);
                            location.reload();
                        })
                    }
                }
            );
        }


        function toggleLoginStatus(loggedIn) {
            if (loggedIn) {
                $('.not-logged-in').hide();
                $('.logged-in').show('slow');
            } else {
                $('.not-logged-in').show('slow');
                $('.logged-in').hide();
            }
        }

        function displayAuthResult(authResult) {
            console.log("AuthResult", authResult); //print user information to the console

            $('#yammer-login').innerHTML = 'Welcome to Yammer!';
            toggleLoginStatus(true);

            $('#authResult').html('Auth Result:<br/>');
            for (var field in authResult) {
                $('#authResult').append(' ' + field + ': ' +
                    authResult[field] + '<br/>');
            }
            $('#authOps').show('slow');

        }

        function getCurrentUser() {
            yam.platform.request({
                // yam.request({
                url: "users/current.json", //this is one of many REST endpoints that are available
                method: "GET",
                data: {},
                success: function (user) { //print message response information to the console
                    console.log("User request was successful.");
                    console.dir(user);
                    toggleLoginStatus(true);
                    $('#authResult').html('User Result:<br/>');
                    for (var field in user) {
                        $('#authResult').append(' ' + field + ': ' +
                            escape(user[field]) + '<br/>');
                    }

                },
                error: function (user) {
                    console.error("There was an error with the request.");
                }
            });

        }

        function getCurrentGroups() {
            yam.platform.request({
                // yam.request({
                url: "groups.json?mine=1",
                method: "GET",
                data: {},
                success: function (group) {
                    $mygroup = "";
                    for ($i = 0; $i < group.length; $i++) {
                        $mygroup += '<img src="' + group[$i].mugshot_url + '">' + " " + group[$i].full_name + "," + "<br>";

                    }
                    $("#current-groups").html($mygroup);
                },

                error: function (group) {
                    console.error("There was an error with the request.");
                }
            });
        }

        function login() {
            console.log("Trigger LoginStatus");
            yam.platform.getLoginStatus(
                function (response) {
                    if (response.authResponse) {
                        console.log("Logged in");
                        displayAuthResult(response.access_token);
                        localStorage.setItem(1, JSON.stringify(response.access_token.token).replace(/"/g, ""));
                    } else {
                        console.log("Not logged in.  Going to login now.");
                        yam.platform.login(function (response) { //prompt user to login and authorize your app, as necessary
                            if (response.authResponse) {
                                displayAuthResult(response.access_token);
                                localStorage.setItem(1, JSON.stringify(response.access_token.token).replace(/"/g, ""));
                            }
                        });
                    }
                }
            );

        }

        function logout() {
            yam.platform.getLoginStatus(
                function (response) {
                    if (response.authResponse) {
                        yam.platform.logout(function (response) {
                            console.log("User was logged out");
                            location.reload();
                        })
                    } else {
                        toggleLoginStatus(false);
                    }
                }
            );
        }

        $(document).ready(function () {

            $('#disconnect').click(logout);
            $('#yammer-js-login-button').click(login);
            $('#yammer-user-button').click(getCurrentUser);
            $('#yammer-group-button').click(getCurrentGroups);

        });
    </script>
    <div id='page'>
        <div>
            <h2>JS SDK</h2>
            <button id="yammer-js-login-button">JS Login</button>
            <button id="yammer-user-button">Get Current User</button>
            <button id="yammer-group-button">Get Current Groups</button>

        </div>

        <div class="logged-in" style="display:none">
            <p>User is now signed in to the app using Yammer</p>
            <button id="disconnect" class="yj-btn yj-btn-alt">Log out from your Yammer account</button>
        </div>
        <div class="logged-in" style="display:none">
            <h2>Authentication Logs</h2>
            <pre id="authResult"></pre>
        </div>
    </div>
    <pre><code>yam.platform.getLoginStatus(callback, [forceRefresh])
</code></pre>

    <p>Determines whether the current user is logged in to Yammer and connected to your application. The first time the method is called, it will query the Yammer API and when this responds <code>callback</code> will be invoked with the server response as
        the only argument. Calling the method subsequently will return the cached response unless <code>forceRefresh</code> is set to <code>true</code>.</p>

    <p>Subsequent calls to <code>yam.platform.request</code> will automatically use the token returned by this call.</p>

    <h2>
<a name="login" class="anchor" href="#login"><span class="mini-icon mini-icon-link"></span></a>login</h2>

    <pre><code>yam.platform.login([opts], [callback])
</code></pre>

    <p>Triggers a login dialog popup. When the login flow is completed the <code>callback</code>
        will be invoked with the access token as the only argument.</p>

    <p>The dialog is located at <a href="https://www.yammer.com/dialog/oauth">https://www.yammer.com/dialog/oauth</a>.
        <br>If <code>opts.loginType</code> == <code>session</code> the dialog used is
        <a href="https://www.yammer.com/dialog/weblogin">https://www.yammer.com/dialog/weblogin</a>, although it's unclear if this is used.</p>

    <h2>
<a name="logout" class="anchor" href="#logout"><span class="mini-icon mini-icon-link"></span></a>logout</h2>

    <pre><code>yam.platform.logout([callback])
</code></pre>

    <p>Logs the user out based on their current bearer token. The <code>callback</code> will be invoked with a single boolean argument to indicate whether logout succeeded or failed.
    </p>

    <h2>
<a name="request" class="anchor" href="#request"><span class="mini-icon mini-icon-link"></span></a>request</h2>

    <pre><code>yam.platform.request(options)
</code></pre>

    <p>Makes requests to the Yammer API using the bearer token provided through
        <code>yam.platform.setAuthToken</code>, retrieved for the users current session via
        <code>yam.platform.getLoginStatus</code> or returned by the login flow triggered by
        <code>yam.platform.login</code>. The <code>options</code> argument mirrors jQuery Ajax, with the addition that if <code>url</code> is relative it will be automatically qualified to the API domain.</p>
    <div id="embedded-follow"></div>
    <div id="embedded-like"></div>
    <div id="current-groups"></div>
    <div id="embedded-feed" style="height:400px;width:500px;"></div>
    <script>
        yam.connect.embedFeed({
            container: '#embedded-feed',
            feedType: 'open-graph',
            feedId: '',
            config: {
                use_sso: false,
                header: true,
                footer: true,
                showOpenGraphPreview: false

            },
            objectProperties: {
                url: '',
                type: 'page'
            }
        });

        yam.connect.actionButton({
            container: "#embedded-follow",
            action: "follow"
        });

        yam.connect.actionButton({
            container: "#embedded-like",
            action: "like"
        });
    </script>
    <a href="/auth.html">Link to page with setAuthToken</a>
</body>

</html>
