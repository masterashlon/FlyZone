<template>
    <div class="modal-container" v-show="visible">
        <div class="pop-up">
            <div class="message">
                <h2>We value your privacy</h2>
                <p>
                    We use cookies to enhance your browsing experience,
                    serve personalized content and use your information just 
                    for use website in its complete form. <br>
                    By clicking "Accept" you consent to our use of cookies.
                </p>
            </div>
            <div class="button-area">
                <a id="accept" @click="accepted(); visible = !visible">Accept</a>
                <a id="refuse" @click="refused(); visible = !visible">Refuse</a>
                
            </div>
            
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                visible: false
            }
        },
        components: {
        },
        methods: {
            storageAvailable(type) {  // test for browser storage function compatibility(no cookie)
           
                let storage;
                try {
                    storage = window[type];
                    const x = '__storage_test__';
                    storage.setItem(x, x);
                    storage.removeItem(x);
                    return true;
                }
                catch (e) {
                    return e instanceof DOMException && (
                        // everything except Firefox
                        e.code === 22 ||
                        // Firefox
                        e.code === 1014 ||
                        // test name field too, because code might not be present
                        // everything except Firefox
                        e.name === 'QuotaExceededError' ||
                        // Firefox
                        e.name === 'NS_ERROR_DOM_QUOTA_REACHED') &&
                        // acknowledge QuotaExceededError only if there's something already stored
                        (storage && storage.length !== 0);
                }
            },
            refused() {
                if(this.storageAvailable('localStorage')){
                    localStorage.setItem("Cookie", "Refused");} 
            },
            accepted() {
                if(this.storageAvailable('localStorage')){
                    localStorage.setItem("Cookie", "Accepted"); }
            }
        },
        beforeMount() {
            window.setTimeout(() => { 
                if(localStorage.getItem("Cookie") === null || localStorage.getItem("Cookie") === "Refused")
                    this.visible = true
             } , 2000);
        },
        mounted() {
            if (this.storageAvailable('localStorage')) {
                //"Yippee! We can use localStorage awesomeness"
                if(localStorage.getItem("Cookie") !== null && localStorage.getItem("Cookie") === "Accepted") { 
                     this.visible = false;
                }
            }
            //console.log(localStorage.getItem("Cookie"))
        }

    }
</script>


<style scoped>
    .modal-container {
        font-family: Arial;
        color: lightgray;
        background: hsla(0 0% 0% / 0.4 );
        position: absolute;
        top: 0;
        left: 0;
        z-index: 2;
        width: 100vw;
        height: 100vh;
    }
    .pop-up {
        background: black;
        position: absolute;
        bottom: 0;
        width: 100vw;
        height: max-content;
        padding: 2rem;
        display: flex;
        gap: 2rem;
    }

    .message {
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .button-area {
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }
    .button-area a {
        display: grid;
        place-items: center;
        border: 1px aqua  solid;
        border-radius: 5px;
        width: 6rem;
        height: 2rem;
    }

    #accept {
        color: white;
        text-shadow: 0 0 1px blue;
        background: aqua;
    }

    #refuse {
        color:aqua;
        background: black;
    }
    .button-area a:hover,
    .button-area a:focus,
    .button-area a:active {
        filter: brightness(80%);
        cursor: pointer;
    }
</style>