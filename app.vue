<script setup>
import { ref, computed } from 'vue';
import CalendarCustom from "~/components/calendar-custom.vue";
</script>

<template>
    <div class="main-container">
        <div class="hero-section">
            <div class="hero-content">
                <h1>Miguel E. Uicab Canabal</h1>
                <span>
                    Calendario Con NUXT y PrimeVue, para entrevista técnica. Puede visitar el repositorio en
                    <a href="https://github.com/MiguelMort09/Calendar-PrimeVue.git" target="_blank" class="doc-text">
                        GITHUB
                    </a>
                </span>
            </div>
        </div>
        <CalendarCustom/>
    </div>
</template>

<style scoped>
::selection {
    background-color: #4ac58e79;
    color: #fff;
}

:focus,
:focus-visible {
    outline: none;
    box-shadow: 0 0 0 2px #1e2226, 0 0 0 4px #41b88395, 0 1px 2px 0 black;
}

a {
    cursor: pointer;
}

.main-container {
    width: max-content;
    margin: 0 auto;
    padding: 5.5rem 6rem 3rem;
}

.hero-section {
    display: flex;
    flex-direction: column;
    gap: 2.267rem;
    margin-bottom: 3.89rem;
}

.hero-section .logo {
    display: flex;
    align-items: center;
    gap: 1.25rem;
}

.hero-section .hero-content {
    display: flex;
    flex-direction: column;
    gap: 1.25rem;
}

.hero-section .hero-content h1 {
    margin: 0;
    font-size: 3rem;
    font-weight: 600;
    max-width: 34.71rem;
}

.hero-section .hero-content span {
    font-size: 1rem;
}

.hero-section .hero-content span > a {
    font-size: 1rem;
    opacity: 0.98;
    color: #41b883;
    font-weight: 700;
    text-decoration-line: underline;
    transition: opacity 0.2s;
}

.hero-section .hero-content span > a:hover {
    opacity: 0.78;
}

@media (max-width: 786px) {
    .main-container {
        padding-left: 4rem;
        padding-right: 4rem;
    }
}

@media (max-width: 486px) {
    .main-container {
        padding-left: 2rem;
        padding-right: 2rem;
    }
}

@keyframes blink {
    0% {
        -webkit-mask-position: 150%;
    }

    100% {
        -webkit-mask-position: -50%;
    }
}
</style>
