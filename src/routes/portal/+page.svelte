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
    onMount(async () => {
        const pkg = await import("peerjs");
        Peer = pkg.default;
        
        
        let mainPeer = localStorage.getItem("mainPeer")
        if (mainPeer != null) {
            peer = new Peer(mainPeer);
            conn.on("open", function() {

                conn.on("data", function(data) {
                    if (data[0] == mainPeer+"-test" && data[1] == "test") {
                        if (data[2] == "init check") {
                            console.log("handshake from portal successful")
                            conn.send([mainPeer+"-test", "main server", "handshake"]);
                        }
                    }
                })

                conn.on("error", function(err) {
                    console.log(err);
                })
            })


        }
        else {
            window.location.href = base;
            return 0;
        }
    })

    

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

