<template>
    <header>
        <a class="center" href="javascript:void(0)">
            <img aria-hidden="true" src="../assets/logo.png" alt="weblogo" />
            <h1 aria-hidden="true">FlyZone</h1>
        </a>
        <div class="menu-logo" @click="openMenu">
            <img src="../assets/sidemenu.png" alt="openmenu icon" />
        </div>
        <ul class="links">
            <li><a href="javascript:void(0)">One</a></li>
            <li><a href="javascript:void(0)">Two</a></li>
            <li><a href="javascript:void(0)">Three</a></li>
        </ul>
    </header>
    <div class="sidebar">
        <div class="close-menu" @click="closeMenu">
            <img src="../assets/sidemenu.png" alt="closemenu icon" />
        </div>
        <ul>
            <li><a href="javascript:void(0)">One</a></li>
            <li><a href="javascript:void(0)">Two</a></li>
            <li><a href="javascript:void(0)">Three</a></li>
        </ul>
    </div>
</template>

<script>
    export default {
        components: {
        },
        methods: {
            openMenu() {
                const sidebar = document.querySelector(".sidebar");
                window.setTimeout(() => sidebar.style.display = "block", 100);
                window.setTimeout(() => sidebar.style.transform = "none", 200);
                
            },
            closeMenu() {
                const sidebar = document.querySelector(".sidebar");
                sidebar.style.transform = "translateX(100%)";
                window.setTimeout(() => sidebar.style.display = "none", 900);
            }
        }


    }
</script>

<style scoped>
    a,
    a:hover,
    a:active,
    a:visited,
    a:focus {
        text-decoration: none;
        color: black;
    }
    header {
        color: black;
        width: 100vw;
        height: 4rem;
        background: linear-gradient(90deg, rgb(0, 209, 0) 60%, rgb(111, 222, 0) 90%);
        display: flex;
        justify-content: center;
        align-items: center;
        padding: .3rem 1.5rem;
    }

    .center {
        display: flex;
        align-items: center;
        gap: .6rem;
    }
    .center img {
        margin: 1rem 0 0 0 ;
        height: 5rem;
        z-index: 2;
    }


    h1 {
        font-size: 3.5rem;
        text-transform: uppercase;
    }
    .center:hover {
        cursor: pointer;
    }

    .links {
        position: absolute;
        right: 2vw;
        display: flex;
        gap: .5rem;
    }

    .links li {
        padding: .2rem .3rem;
        border-radius: 10px;
    }
    .links li:hover {
        background: lightgray;
    }

    .menu-logo {
        display: none;
    }

    .menu-logo img,
    .close-menu img{
        height: 2rem;
    }

    .sidebar {
        position:absolute;
        right: 0;
        padding: 1.2rem;
        height: max-content;
        width: max-content;
        background: rgb(111, 222, 0);
        border-bottom-left-radius: 15px;
        transform: translateX(100%);
        transition: transform 500ms;
        z-index: 2;
        display: none;
    }
    .close-menu img{
        transform: rotateY(180deg);
    }

    ul {
        list-style: none;
    }

    ul li {
        font-size: 1.5rem;
        padding: .2rem 0;
    }
    .sidevar ul li a:hover {
        color: rgb(168, 168, 168);
    } 
    @media screen and (max-width: 1200px){
        .center img {
            margin: 0;
            height: 3.5rem;
            z-index: 1;
        }
    }
    @media screen and (max-width: 900px){
        header {
            justify-content: center;
        }

        .center {
            margin-left: 0;
        }

        .links {
            display: none;
        }

        .menu-logo {
            display: block;
            position: absolute;
            right: 1rem;
        }
        .sidebar {
            display: block;
        }

    }
    
    @media screen and (max-width: 600px){
        header {
            height: 2.8rem;
        }

        .center img {
            height: 2.5rem;
        }

        h1 {
            font-size: 1.8rem;
        }
       
    }
</style>