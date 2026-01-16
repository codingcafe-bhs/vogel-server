<script>
    import { onMount } from "svelte";
    import { fly, slide } from "svelte/transition";
    import { base } from "$app/paths";
    let Peer;

    let toggle = $state(false);
    

    onMount(() => {
        setTimeout(() => {
            toggle = true;
        }, 100);
    })

    let code = $state("");

    let peer;
    let peerConnection;
    let connectionStatus = $state("disconnected");
    
    onMount(async () => {
        const pkg = await import("peerjs");
        Peer = pkg.default;
        
        let mainPeer = localStorage.getItem("mainPeer");
        if (mainPeer != null) {
            peer = new Peer(mainPeer);
            peer.on("open", function(id) {
                console.log("PeerJS ID:", id);
            })

            // dev purposes

            peer.on("connection", function(conn) {
                conn.on("data", function(data) {
                    console.log("Received data on incoming connection:", data);
                });
            })

            //

            peer.on("error", function(err) {
                console.log(err);
            })
        }
        else {
            window.location.href = base;
            return 0;
        }
    });
    
    function setupConnectionHandlers(conn) {
        conn.on("open", function() {
            console.log("Connection established with:", conn.peer);
            peerConnection = conn;
            connectionStatus = "connected";
            
            // Send initial handshake
            conn.send([localStorage.getItem("mainPeer") + "-test", "portal", "init check"]);
        });
        
        conn.on("data", function(data) {
            console.log("Received data:", data);
            if (Array.isArray(data) && data.length >= 3) {
                if (data[2] == "handshake") {
                    console.log("Handshake from main server successful");
                }
            }
        });
        
        conn.on("error", function(err) {
            console.error("Connection error:", err);
            connectionStatus = "error";
        });
        
        conn.on("close", function() {
            console.log("Connection closed");
            connectionStatus = "disconnected";
        });
    }

    

    /*
    function processCode() {
        let peerConnect = "codingcafe-" + code.substring(0,1) + "-";
        peerConnect += "" + (parseInt(code.substring(1))*67);
        sessionStorage.setItem("mainPeer", peerConnect);
        console.log(peerConnect);
    }
        */
</script>
<svelte:head>
    <title>Vogel Hub</title>
</svelte:head>

<h1>VOGEL HUB</h1>
<p style="margin-bottom: 50px;">Coding Café Competition Portal</p>

