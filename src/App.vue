<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

// Import Swiper.js components and modules
import { Swiper, SwiperSlide } from 'swiper/vue';
import { Autoplay, EffectFade } from 'swiper/modules';
import 'swiper/css';
import 'swiper/css/effect-fade';

// Import Animate on Scroll (AOS)
import AOS from 'aos';
import 'aos/dist/aos.css';

// --- Reactive State ---
const scrolled = ref(false);
const address = ref('Birkenweg 20, 9490 Vaduz');
const swiperModules = [Autoplay, EffectFade];

// --- Component Data ---

// Select 4 images for the hero carousel
const heroImages = [
  'src/assets/gallery/preview-7.jpg',
  'src/assets/gallery/preview-2.jpg',
  'src/assets/gallery/preview-5.jpg',
  'src/assets/gallery/preview-9.jpg',
];

// Programmatically generate paths for all 9 gallery images
const galleryImages = Array.from({ length: 9 }, (_, i) => ({
  src: `src/assets/gallery/preview-${i + 1}.jpg`,
  alt: `Professionelle Gartenarbeit ${i + 1}`
}));

const services = [
  // ... (service data is unchanged, keeping it for brevity)
  { title: 'Gartenunterhalt', description: 'Professionelle Pflege von Rasen, Hecken, Beeten und Bäumen für eine ganzjährig prächtige Grünanlage.', icon: '<svg class="w-12 h-12 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2h1a2 2 0 002-2v-1a2 2 0 012-2h1.945M7.688 3.688A1.5 1.5 0 018.25 3h7.5a1.5 1.5 0 011.063.438l3.688 3.688a1.5 1.5 0 01.438 1.062v7.5a1.5 1.5 0 01-1.5 1.5h-15a1.5 1.5 0 01-1.5-1.5v-7.5a1.5 1.5 0 01.438-1.062l3.688-3.688zM12 11v5m0 0l-2-2m2 2l2-2"></path></svg>' },
  { title: 'Hauswartung', description: 'Umfassende Betreuung Ihrer Immobilie, von kleinen Reparaturen bis zur Überwachung der Haustechnik.', icon: '<svg class="w-12 h-12 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"></path></svg>' },
  { title: 'Innenreinigung', description: 'Gründliche und zuverlässige Reinigung Ihrer Wohn- und Arbeitsräume für ein makelloses und hygienisches Zuhause.', icon: '<svg class="w-12 h-12 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-12v4m-2-2h4m5 4v4m-2-2h4M5 3l14 18"></path></svg>' }
];


// --- Methods ---
const handleScroll = () => {
  scrolled.value = window.scrollY > 50;
};

// --- Lifecycle Hooks ---
onMounted(() => {
  AOS.init({ once: true, duration: 800, easing: 'ease-in-out-cubic' });
  window.addEventListener('scroll', handleScroll);
});

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleScroll);
});
</script>

<template>
  <div>
    <!-- Header / Navigation -->
    <header class="fixed w-full z-30 top-0 text-white transition duration-300 ease-in-out" :class="{ 'bg-gray-900 bg-opacity-90 shadow-2xl': scrolled }">
      <div class="container mx-auto flex items-center justify-between p-4">
        <a href="#home" class="flex items-center">
          <img src="@/assets/logo/logo.png" alt="Morina Logo" class="h-12 w-auto mr-4">
          <span class="font-bold text-xl tracking-tight">Morina Gartenunterhalt</span>
        </a>
        <nav class="hidden md:flex items-center space-x-8">
          <a href="#services" class="hover:text-green-400 transition">Leistungen</a>
          <a href="#philosophy" class="hover:text-green-400 transition">Philosophie</a>
          <a href="#gallery" class="hover:text-green-400 transition">Galerie</a>
          <a href="#contact" class="hover:text-green-400 transition">Kontakt</a>
        </nav>
      </div>
    </header>

    <!-- Hero Section with Swiper Carousel -->
    <section id="home" class="relative h-screen w-full flex items-center justify-center text-center">
      <swiper
        :modules="swiperModules"
        :slides-per-view="1"
        :loop="true"
        :effect="'fade'"
        :autoplay="{ delay: 5000, disableOnInteraction: false }"
        class="absolute inset-0 w-full h-full"
      >
        <swiper-slide v-for="(image, index) in heroImages" :key="index">
          <div class="w-full h-full bg-cover bg-center" :style="{ backgroundImage: `url(${image})` }">
            <div class="w-full h-full bg-black bg-opacity-50"></div>
          </div>
        </swiper-slide>
      </swiper>

      <div class="relative z-10 px-4 text-white">
        <h1 class="text-5xl md:text-7xl font-bold mb-4" data-aos="fade-up" data-aos-delay="200" style="text-shadow: 2px 2px 8px rgba(0,0,0,0.7);">
          Natur. Präzision. Perfektion.
        </h1>
        <p class="text-xl md:text-2xl text-gray-200 mb-8" data-aos="fade-up" data-aos-delay="400" style="text-shadow: 1px 1px 4px rgba(0,0,0,0.7);">
          Ihr Experte für eine makellose Umgebung in Vaduz.
        </p>
        <div data-aos="fade-up" data-aos-delay="600">
          <a href="#services" class="bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-8 rounded-full transition duration-300 ease-in-out transform hover:scale-105">
            Entdecken Sie unsere Leistungen
          </a>
        </div>
      </div>
    </section>

    <!-- Services Section -->
    <section id="services" class="py-20 section-bg">
        <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">
                Unsere <span class="text-glow-green">Leistungen</span>
            </h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-12">
                <div v-for="(service, index) in services" :key="service.title" class="bg-gray-800 p-8 rounded-lg text-center card-hover" :data-aos="'fade-up'" :data-aos-delay="index * 100">
                    <div class="bg-gray-900 rounded-full w-24 h-24 flex items-center justify-center mx-auto mb-6" v-html="service.icon"></div>
                    <h3 class="text-2xl font-bold mb-4 text-white">{{ service.title }}</h3>
                    <p class="text-gray-400">{{ service.description }}</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Philosophy Section -->
    <section id="philosophy" class="py-20 bg-gray-900">
        <div class="container mx-auto px-4 text-center" data-aos="fade-in" data-aos-duration="1000">
            <h2 class="text-4xl font-bold text-center mb-4 text-white">Unsere <span class="text-glow-yellow">Philosophie</span></h2>
            <p class="text-xl text-gray-300 max-w-4xl mx-auto leading-relaxed">
            "WIR PFLEGEN IHR HAUS, IHREN GARTEN, DIE INNENREINIGUNG IHRES ZUHAUSE UND IHRE UMWELT. WÄHLEN SIE UNS, WENN SIE EINE SAUBERE UMGEBUNG WOLLEN."
            </p>
        </div>
    </section>

    <!-- Gallery Section -->
    <section id="gallery" class="py-20 section-bg">
        <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">
            Visuelle <span class="text-glow-green">Impressionen</span>
            </h2>
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
            <div v-for="(image, index) in galleryImages" :key="index" class="aspect-w-1 aspect-h-1 overflow-hidden rounded-lg shadow-lg" :data-aos="'zoom-in-up'" :data-aos-delay="(index % 3) * 100">
                <img :src="image.src" :alt="image.alt" class="w-full h-full object-cover transition duration-500 ease-in-out transform hover:scale-110">
            </div>
            </div>
        </div>
    </section>

    <!-- Contact & Footer Sections -->
    <section id="contact" class="py-20 bg-gray-900">
        <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">Kontaktieren Sie <span class="text-glow-yellow">Uns</span></h2>
            <div class="flex flex-wrap -mx-4">
                <div class="w-full lg:w-1/2 px-4 mb-8 lg:mb-0" data-aos="fade-right"><div class="h-96 rounded-lg overflow-hidden shadow-2xl"><iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2726.938162235315!2d9.516949315598918!3d47.14152497915668!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x479b31174af97299%3A0x6b7b7a698a69a239!2sBirkenweg%2020%2C%209490%20Vaduz%2C%2C%20Liechtenstein!5e0!3m2!1sen!2sus!4v1678886452131!5m2!1sen!2sus" width="100%" height="100%" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe></div></div>
                <div class="w-full lg:w-1/2 px-4 flex items-center" data-aos="fade-left"><div class="bg-gray-800 p-8 rounded-lg w-full"><h3 class="text-2xl font-bold mb-4 text-white">Morina Gartenunterhalt</h3><p class="text-gray-400 mb-4">Bereit, Ihre Umgebung zu verwandeln? Wir sind für Sie da.</p><div class="space-y-4"><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"></path></svg><span class="text-lg">{{ address }}</span></div><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path></svg><a href="mailto:info@morina-garten.li" class="text-lg hover:text-green-400 transition">info@morina-garten.li</a></div><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"></path></svg><a href="tel:+4231234567" class="text-lg hover:text-green-400 transition">+423 123 45 67</a></div></div></div></div>
            </div>
        </div>
    </section>
    <footer class="bg-gray-900 border-t-2 border-green-500">
        <div class="container mx-auto py-4 px-5 text-center text-gray-400">
            <p>© {{ new Date().getFullYear() }} Morina Gartenunterhalt. Alle Rechte vorbehalten.</p>
            <p class="text-sm">{{ address }}</p>
        </div>
    </footer>
  </div>
</template>