<script>
    import { onMount } from "svelte";
    import { fly, slide } from "svelte/transition";
    import { base } from "$app/paths";

    let toggle = $state(false);
    onMount(() => {
        setTimeout(() => {
            toggle = true;
        }, 100);
        setTimeout(() => {
            document.getElementById("codeInput").focus();
        }, 1100)
    })

    let code = $state("");
    let group = $state("");
    let numbers = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"]
    let errorMessage = $state("")

    onMount(() => {
        if (localStorage.getItem("competitionCode") != null) {
            code = localStorage.getItem("competitionCode");
        }
    })

    function processCode() {
        errorMessage = "";
        if (code.length != 5) {
            code = "";
            errorMessage = "(Invalid Code)";
            setTimeout(() => {
                document.getElementById("codeInput").focus();
            }, 1000)
            return false; 
        }
        for (let i = 0; i < code.length; i++) {
            if (numbers.indexOf(code.substring(i, i+1)) == -1) {
                code = "";
                errorMessage = "(Invalid Code)";
                setTimeout(() => {
                    document.getElementById("codeInput").focus();
                }, 1000)
                return false; 
            }
        }
        let peerConnect = "codingcafe-" + code.substring(0,1) + "-";
        peerConnect += "" + (parseInt(code.substring(1))*67);
        if (localStorage.getItem("competitionCode") != code) {
            for (let i = 0; i < localStorage.length; i++) {
                localStorage.removeItem(localStorage.key(i));
                i--;
            }
        }
        localStorage.setItem("competitionCode", code)
        localStorage.setItem("mainPeer", peerConnect);
        window.location.href = base + "/portal";
        //window.location.href = base + "/onboard"
        console.log(peerConnect);
    }
</script>
<svelte:head>
    <title>Vogel Hub</title>
</svelte:head>

<h1>VOGEL HUB</h1>
<p style="margin-bottom: 50px;">Coding Café Competition Portal</p>

{#if toggle}
<form transition:fly={{y:200}} onsubmit={processCode}>
    <h2 style:font-family="Gabarito, Space Grotesk, Montserrat, Futura;">Create/Load Competition Code</h2>
    <p><i>{errorMessage}</i></p>
    <input required bind:value={code} style="margin-bottom: 30px;" class="large" id="codeInput"/>
    {#if code.length == 5 && localStorage.getItem("competitionCode") != code}<p>This will create a new competition and reset all saved data</p>
    {:else if code.length==5}<p>This will load the saved competition data</p>{/if}
    {#if code.length == 5}<input transition:slide style="margin-bottom: 30px; font-size: 18px;" type="submit" value="Continue">{/if}
    
</form> 
{/if}