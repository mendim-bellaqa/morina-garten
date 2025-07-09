<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
import { Autoplay, EffectFade } from 'swiper/modules';
import AOS from 'aos';
import 'swiper/css';
import 'swiper/css/effect-fade';
import 'aos/dist/aos.css';

const galleryImageModules = import.meta.glob('@/assets/gallery/*.{jpg,jpeg}', { eager: true });
const teamImageModules = import.meta.glob('@/assets/team/*.{jpg,jpeg}', { eager: true });
const getImageUrl = (module) => module?.default || '';
const findImageModule = (modules, name) => Object.keys(modules).find(p => p.includes(`/${name}.`));

const address = ref('Heiligkreuz 36 9490 Vaduz, Liechtenstein');
const swiperModules = [Autoplay, EffectFade];
const heroContainer = ref(null);
const parallax = ref({ rotateX: 0, rotateY: 0, bgPosX: '50%', bgPosY: '50%' });
const isMobileMenuOpen = ref(false);
const isScrolledPastHero = ref(false);
const isGalleryOpen = ref(false);
const activeImageIndex = ref(0);

const heroImages = ref(Object.values(galleryImageModules).map(getImageUrl));
const galleryImages = computed(() => Object.values(galleryImageModules).map(module => ({ src: getImageUrl(module), alt: 'Professionelle Gartenarbeit' })));
const teamMembers = [
    { name: 'Morina Jeton', role: 'Inhaber & Geschäftsführer', imageSrc: getImageUrl(teamImageModules[findImageModule(teamImageModules, '1')]) },
    { name: 'Christoph Mix', role: 'Spezialist für Gartenunterhalt', imageSrc: getImageUrl(teamImageModules[findImageModule(teamImageModules, '2')]) },
    { name: 'Morina Shkurta', role: 'Expertin für Hauswartungen', imageSrc: getImageUrl(teamImageModules[findImageModule(teamImageModules, '3')]) },
];
const heroHeadline = "Natur. Präzision. Perfektion.";
const heroHeadlineWords = computed(() => heroHeadline.split(' '));
const leistungen = [
  { title: 'Gartenunterhalt', description: 'Wir pflegen Ihren Garten mit Sorgfalt und Fachwissen – Rasen, Hecken, Beete, Bäume und Blumen. Vom Mähen bis zur Blütenpflege sorgen wir das ganze Jahr für eine gesunde, gepflegte und sehenswerte Grünanlage.', type: 'garten' },
  { title: 'Hauswartung', description: 'Wir pflegen Ihr Zuhause mit Liebe zum Detail – von der gründlichen Innenreinigung bis zur gepflegten Außenumgebung. Wählen Sie uns, wenn Ihnen Sauberkeit, Ordnung und Wohlbefinden wichtig sind.', type: 'haus' },
  { title: 'Innenreinigung', description: 'Gründliche und zuverlässige Reinigung Ihrer Wohn- und Arbeitsräume für eine saubere und hygienische Umgebung.', type: 'reinigung' }
];

const openGallery = (index) => { activeImageIndex.value = index; isGalleryOpen.value = true; };
const closeGallery = () => { isGalleryOpen.value = false; };
const nextImage = () => { activeImageIndex.value = (activeImageIndex.value + 1) % galleryImages.value.length; };
const prevImage = () => { activeImageIndex.value = (activeImageIndex.value - 1 + galleryImages.value.length) % galleryImages.value.length; };
const currentImage = computed(() => galleryImages.value[activeImageIndex.value]?.src);
const previousImage = computed(() => galleryImages.value[(activeImageIndex.value - 1 + galleryImages.value.length) % galleryImages.value.length]?.src);
const nextImageSrc = computed(() => galleryImages.value[(activeImageIndex.value + 1) % galleryImages.value.length]?.src);

const handleMouseMove = (event) => {
  if (!heroContainer.value) return;
  const { clientX, clientY } = event;
  const { offsetWidth, offsetHeight } = heroContainer.value;
  const x = (clientX - offsetWidth / 2) / offsetWidth;
  const y = (clientY - offsetHeight / 2) / offsetHeight;
  const maxRotate = 8;
  parallax.value = { rotateX: -y * maxRotate, rotateY: x * maxRotate, bgPosX: (50 + x * 5) + '%', bgPosY: (50 + y * 5) + '%' };
};
const handleScroll = () => { isScrolledPastHero.value = window.scrollY > 50; };
const handleKeydown = (e) => { if (isGalleryOpen.value) { if (e.key === 'Escape') closeGallery(); if (e.key === 'ArrowLeft') prevImage(); if (e.key === 'ArrowRight') nextImage(); } };
watch(isGalleryOpen, (newVal) => { document.body.style.overflow = newVal ? 'hidden' : ''; });
watch(isMobileMenuOpen, (newVal) => { document.body.style.overflow = newVal ? 'hidden' : ''; });
onMounted(() => {
  AOS.init({ once: true, duration: 1000, easing: 'ease-out-cubic', offset: 50 });
  window.addEventListener('mousemove', handleMouseMove);
  window.addEventListener('scroll', handleScroll, { passive: true });
  window.addEventListener('keydown', handleKeydown);

  const parallaxSections = document.querySelectorAll('.parallax-section');
  const onScroll = () => {
    parallaxSections.forEach(section => {
      const speed = section.dataset.parallaxSpeed || 0.3;
      const offset = window.scrollY * speed;
      section.style.backgroundPosition = `center ${offset}px`;
    });
  };
  window.addEventListener('scroll', onScroll);
  onScroll();
  onUnmounted(() => window.removeEventListener('scroll', onScroll));
});
onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove);
  window.removeEventListener('scroll', handleScroll);
  window.removeEventListener('keydown', handleKeydown);
});
</script>

<template>
  <div>
    <header class="sticky top-0 z-50 w-full backdrop-blur-xl transition-colors duration-300 ease-in-out" :class="{ 'bg-transparent': !isScrolledPastHero, 'bg-black shadow-2xl border-b border-gray-800': isScrolledPastHero }">
      <div class="container mx-auto flex items-center justify-between p-4 text-white">
        <a href="#home" class="flex items-center"><img src="@/assets/logo/logo.png" alt="Morina Logo" class="h-10 w-auto mr-3"><span class="font-bold text-lg tracking-tight">Morina Gartenunterhalt</span></a>
        <nav class="hidden md:flex items-center space-x-10 text-sm uppercase font-semibold tracking-wider">
          <a href="#leistungen" class="hover:text-green-400 transition-colors duration-300 px-3 py-1 rounded-lg hover:bg-gray-800/50">Leistungen</a>
          <a href="#informationen" class="hover:text-green-400 transition-colors duration-300 px-3 py-1 rounded-lg hover:bg-gray-800/50">Informationen</a>
          <a href="#team" class="hover:text-green-400 transition-colors duration-300 px-3 py-1 rounded-lg hover:bg-gray-800/50">Unser Team</a>
          <a href="#gallery" class="hover:text-green-400 transition-colors duration-300 px-3 py-1 rounded-lg hover:bg-gray-800/50">Galerie</a>
          <a href="#contact" class="hover:text-green-400 transition-colors duration-300 px-3 py-1 rounded-lg hover:bg-gray-800/50">Kontakt</a>
        </nav>
        <div class="md:hidden"><button @click="isMobileMenuOpen = !isMobileMenuOpen" class="z-50 focus:outline-none"><svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path v-if="!isMobileMenuOpen" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" /><path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button></div>
      </div>
    </header>

    <transition name="mobile-menu-fade">
      <div v-if="isMobileMenuOpen" class="fixed inset-0 z-40 bg-black flex flex-col items-center justify-center">
        <nav class="flex flex-col items-center text-center space-y-8">
          <a @click="isMobileMenuOpen = false" href="#leistungen" class="text-2xl font-bold uppercase tracking-wider text-white px-5 py-2 hover:bg-gray-800/50 rounded-lg">Leistungen</a>
          <a @click="isMobileMenuOpen = false" href="#informationen" class="text-2xl font-bold uppercase tracking-wider text-white px-5 py-2 hover:bg-gray-800/50 rounded-lg">Informationen</a>
          <a @click="isMobileMenuOpen = false" href="#team" class="text-2xl font-bold uppercase tracking-wider text-white px-5 py-2 hover:bg-gray-800/50 rounded-lg">Unser Team</a>
          <a @click="isMobileMenuOpen = false" href="#gallery" class="text-2xl font-bold uppercase tracking-wider text-white px-5 py-2 hover:bg-gray-800/50 rounded-lg">Galerie</a>
          <a @click="isMobileMenuOpen = false" href="#contact" class="text-2xl font-bold uppercase tracking-wider text-white px-5 py-2 hover:bg-gray-800/50 rounded-lg">Kontakt</a>
        </nav>
      </div>
    </transition>

    <div class="relative">

      <section id="home" ref="heroContainer" class="h-screen w-full relative overflow-hidden bg-black flex items-center justify-center" style="perspective: 1000px;">
  <!-- Falling leaves background elements -->
  <div class="falling-leaves pointer-events-none absolute inset-0 z-10">
    <!-- Detailed leaves -->
    <svg v-for="n in 10" :key="'leaf'+n"
      class="falling-leaf"
      :style="{
        left: `${Math.random() * 95}%`,
        animationDelay: `${Math.random() * 8}s`,
        animationDuration: `${7 + Math.random() * 4}s`,
        width: `${32 + Math.random() * 24}px`
      }"
      viewBox="0 0 100 100"
      fill="none"
    >
      <path d="M50 10C60 30 80 35 80 50C80 70 50 85 50 85C50 85 20 70 20 50C20 35 40 30 50 10Z" fill="#2ecc71" stroke="#27ae60" stroke-width="2"/>
      <path d="M50 30C55 40 65 45 65 50" stroke="#1e8449" stroke-width="1.5" stroke-linecap="round"/>
    </svg>
    
    <!-- Maple leaves -->
    <svg v-for="n in 6" :key="'maple'+n"
      class="falling-leaf"
      :style="{
        left: `${Math.random() * 95}%`,
        animationDelay: `${Math.random() * 6}s`,
        animationDuration: `${6 + Math.random() * 5}s`,
        width: `${40 + Math.random() * 20}px`
      }"
      viewBox="0 0 100 100"
      fill="none"
    >
      <path d="M50 10C55 25 70 20 70 30C70 40 60 45 50 60C40 45 30 40 30 30C30 20 45 25 50 10Z" fill="#e74c3c" stroke="#c0392b" stroke-width="1.5"/>
      <path d="M50 60L50 85" stroke="#c0392b" stroke-width="2" stroke-linecap="round"/>
    </svg>
    
    <!-- Colorful flowers -->
    <svg v-for="n in 8" :key="'flower'+n"
      class="falling-flower"
      :style="{
        left: `${Math.random() * 95}%`,
        animationDelay: `${Math.random() * 10}s`,
        animationDuration: `${8 + Math.random() * 5}s`,
        width: `${24 + Math.random() * 16}px`
      }"
      viewBox="0 0 100 100"
      fill="none"
    >
      <circle cx="50" cy="50" r="15" :fill="n % 3 === 0 ? '#f1c40f' : n % 3 === 1 ? '#e74c3c' : '#ffffff'"/>
      <ellipse cx="50" cy="20" rx="10" ry="15" fill="#ffffff" opacity="0.8"/>
      <ellipse cx="50" cy="80" rx="10" ry="15" fill="#ffffff" opacity="0.8"/>
      <ellipse cx="20" cy="50" rx="15" ry="10" fill="#ffffff" opacity="0.8"/>
      <ellipse cx="80" cy="50" rx="15" ry="10" fill="#ffffff" opacity="0.8"/>
      <circle cx="50" cy="50" r="8" fill="#2ecc71"/>
    </svg>
  </div>
  
  <swiper :modules="swiperModules" :loop="true" :effect="'fade'" :fadeEffect="{ crossFade: true }" :autoplay="{ delay: 6000, disableOnInteraction: false }" class="absolute inset-0 z-0 w-full h-full">
    <swiper-slide v-for="(image, index) in heroImages" :key="`hero-${index}`">
      <div class="w-full h-full bg-cover bg-center transition-all duration-300 ease-out" :style="{ backgroundImage: `url(${image})` }"></div>
    </swiper-slide>
  </swiper>
  
  <div class="aurora-container">
    <div class="aurora-blob one"></div>
    <div class="aurora-blob two"></div>
    <div class="aurora-blob three"></div>
  </div>
  
  <!-- Centered glass box -->
  <div class="absolute inset-0 z-20 flex items-center justify-center p-4" style="transform-style: preserve-3d;">
    <div class="glass-box-container relative w-full max-w-4xl mx-auto">
      <div class="glass-box-content text-center text-white p-8 md:p-12">
        <h1 class="text-4xl sm:text-5xl md:text-6xl font-bold mb-6 text-white tracking-tight">
          MORINA GmbH
        </h1>
        <h2 class="text-2xl sm:text-3xl md:text-4xl font-semibold mb-6">
          Natur. Präzision. Perfektion.
        </h2>
        <p class="text-lg sm:text-xl md:text-2xl text-gray-200 max-w-2xl mx-auto">
          Ihr Experte für eine makellose und gepflegte Umgebung in Liechtenstein und Umgebung.
        </p>
      </div>
    </div>
  </div>
  
  <div class="absolute bottom-0 left-0 w-full h-1/4 z-10 bg-gradient-to-t from-gray-900 to-transparent"></div>
</section>

      <main class="relative z-30 bg-gray-900 -mt-1">
        <section id="leistungen" class="py-20 bg-black">
          <div class="container mx-auto px-4">
            <h2 
              class="text-4xl font-bold text-center mb-16 text-white" 
              data-aos="fade-up"
            >
              Unsere <span class="text-glow-green">Leistungen</span>
            </h2>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-16">
              <div
                v-for="(service, index) in leistungen"
                :key="service.title"
                class="flex flex-col items-center text-center"
                data-aos="fade-up"
                :data-aos-delay="index * 150"
              >
                <div class="icon-container mb-6">
                  <!-- Garten Icon -->
                  <svg
                    v-if="service.type === 'garten'"
                    class="animated-icon"
                    width="80"
                    height="80"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="#2ecc71"
                    stroke-width="1.5"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  >
                    <path d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2h1a2 2 0 002-2v-1a2 2 0 012-2h1.945"/>
                    <path d="M7.688 3.688A1.5 1.5 0 018.25 3h7.5a1.5 1.5 0 011.063.438l3.688 3.688a1.5 1.5 0 01.438 1.062v7.5a1.5 1.5 0 01-1.5 1.5h-15a1.5 1.5 0 01-1.5-1.5v-7.5a1.5 1.5 0 01.438-1.062l3.688-3.688z"/>
                    <path d="M12 11v5m0 0l-2-2m2 2l2-2"/>
                  </svg>

                  <!-- Haus Icon -->
                  <svg
                    v-else-if="service.type === 'haus'"
                    class="animated-icon"
                    width="80"
                    height="80"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="#2ecc71"
                    stroke-width="1.5"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  >
                    <path d="M3 12l2-2m0 0l7-7 7 7"/>
                    <path d="M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"/>
                  </svg>

                  <!-- Reinigung Icon -->
                  <svg
                    v-else-if="service.type === 'reinigung'"
                    class="animated-icon"
                    width="80"
                    height="80"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="#2ecc71"
                    stroke-width="1.5"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  >
                    <path d="M5 3v4M3 5h4m-1 9v4m-2-2h4m5-12v4m-2-2h4m5 4v4m-2-2h4M5 3l14 18"/>
                  </svg>
                </div>

                <h3 class="text-2xl font-bold mb-3 text-white">
                  {{ service.title }}
                </h3>
                <p class="text-gray-400 leading-relaxed">
                  {{ service.description }}
                </p>
              </div>
            </div>
          </div>
        </section>

        <section id="informationen" class="py-20 section-bg"><div class="container mx-auto px-4 text-center" data-aos="fade-in"><h2 class="text-4xl font-bold text-center mb-4 text-white">Unser <span class="text-glow-yellow">Versprechen</span></h2><p class="text-xl text-gray-300 max-w-4xl mx-auto leading-relaxed">Für ein sauberes, gepflegtes Zuhause, in dem man sich wohlfühlt. Verlassen Sie sich auf unser Team für eine Umgebung, in der Natur und Ästhetik im Einklang sind. Zuverlässigkeit, Präzision und ein Auge fürs Detail – ob Reinigung oder Gartenarbeit: Wir machen's mit Herz und Verstand.</p></div></section>
        
        <section id="team" class="py-20 bg-gray-900">
            <div class="container mx-auto px-4">
                <h2 class="text-4xl font-bold text-center mb-16 text-white" data-aos="fade-up">Unser <span class="text-glow-green">Team</span></h2>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-10">
                    <div v-for="(member, index) in teamMembers" :key="member.name" class="team-card-border p-px rounded-2xl" data-aos="fade-up" :data-aos-delay="100 + index * 150">
                        <div class="bg-gray-800/60 backdrop-blur-md rounded-2xl p-6 text-center h-full flex flex-col"><div class="mb-4 relative"><img :src="member.imageSrc" :alt="member.name" class="w-32 h-32 rounded-full mx-auto object-cover ring-2 ring-white/10"><div class="absolute inset-0 rounded-full border-2 border-transparent group-hover:border-green-400 transition-all"></div></div><h3 class="text-2xl font-bold text-white">{{ member.name }}</h3><p class="text-green-400 font-semibold mt-1">{{ member.role }}</p></div>
                    </div>
                </div>
            </div>
        </section>

        <section id="gallery" class="py-20 section-bg">
          <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">Visuelle <span class="text-glow-green">Impressionen</span></h2>
            <div class="grid grid-cols-2 sm:grid-cols-2 md:grid-cols-3 gap-2 sm:gap-4">
              <div
                v-for="(image, index) in galleryImages"
                :key="`gallery-${index}`"
                class="aspect-w-1 aspect-h-1 overflow-hidden rounded-lg shadow-lg gallery-item cursor-pointer"
                :data-aos="'zoom-in-up'"
                :data-aos-delay="(index % 3) * 100"
                @click="openGallery(index)"
              >
                <img 
                  :src="image.src" 
                  :alt="image.alt" 
                  class="w-full h-full object-cover transition duration-300 ease-in-out hover:scale-105"
                  loading="lazy"
                />
              </div>
            </div>
          </div>
        </section>
``
        <!-- Crystalline Gallery Lightbox -->
        <transition name="lightbox-fade">
          <div v-if="isGalleryOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4 lightbox-backdrop" @click="closeGallery">
            <div class="relative z-10 w-full h-full flex items-center justify-center lightbox-container" @click.stop>
              <!-- Left Arrow -->
              <button
                @click.stop="prevImage"
                class="absolute left-0 md:-left-16 top-1/2 -translate-y-1/2 z-20 p-3 lightbox-nav-button"
                aria-label="Vorheriges Bild"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 md:w-10 md:h-10 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
                </svg>
              </button>
              <!-- Image Wrapper -->
              <div class="lightbox-image-wrapper w-[96vw] max-w-3xl h-[60vh] sm:w-[80vw] sm:h-[80vh] relative flex items-center justify-center">
                <img :src="currentImage" class="lightbox-image current max-w-full max-h-full object-contain rounded-lg" alt="Current Image">
                <!-- Close button -->
                <button
                  @click="closeGallery"
                  style="position: absolute; top: 1rem; right: 1rem; z-index: 30;"
                  class="p-2 rounded-full bg-black/70 hover:bg-green-500 transition group"
                  aria-label="Schließen"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-white group-hover:text-[#2ecc71]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                  </svg>
                </button>
              </div>
              <!-- Right Arrow -->
              <button
                @click.stop="nextImage"
                class="absolute right-0 md:-right-16 top-1/2 -translate-y-1/2 z-20 p-3 lightbox-nav-button"
                aria-label="Nächstes Bild"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 md:w-10 md:h-10 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                </svg>
              </button>
            </div>
          </div>
        </transition>

        <section id="contact" class="py-20 bg-gray-900">
          <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">
              Kontaktieren Sie <span class="text-glow-yellow">Uns</span>
            </h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-8 items-stretch">
              <!-- Text Block -->
              <div class="bg-gray-900/50 backdrop-blur-xl p-8 md:p-12 rounded-2xl shadow-2xl border border-gray-800 flex flex-col justify-center">
                <h3 class="text-3xl font-bold text-white">Wir sind für Sie da</h3>
                <p class="text-gray-300 mt-2 mb-8">Senden Sie uns eine Nachricht oder besuchen Sie uns.</p>
                <div class="space-y-6">
                  <div class="flex items-start">
                    <svg class="w-6 h-6 text-green-400 mr-4 shrink-0 mt-1" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"></path></svg>
                    <span class="text-lg">{{ address }}</span>
                  </div>
                  <div class="flex items-center">
                    <svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path></svg>
                    <a href="mailto:morina.gartenpflege@gmail.com" class="text-lg hover:text-green-400 transition">morina.gartenpflege@gmail.com</a>
                  </div>
                  <div class="flex items-center">
                    <svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"></path></svg>
                    <a href="tel:+41789262407" class="text-lg hover:text-green-400 transition">+41 78 926 24 07</a>
                  </div>
                </div>
              </div>
              <!-- Map Block -->
              <div class="rounded-2xl overflow-hidden shadow-2xl border border-gray-800 min-h-[350px]">
                <iframe
                  src="https://www.google.com/maps?q=Heiligkreuz+36&output=embed&t=k"
                  width="100%"
                  height="100%"
                  style="border:0; min-height:350px;"
                  allowfullscreen=""
                  loading="lazy"
                  referrerpolicy="no-referrer-when-downgrade"
                ></iframe>
              </div>
            </div>
          </div>
        </section>
      </main>

      <footer class="bg-gray-900 border-t-2 border-green-500 relative z-30"><div class="container mx-auto py-4 px-5 text-center text-gray-400"><p>© {{ new Date().getFullYear() }} Morina Gartenunterhalt. Alle Rechte vorbehalten.</p><p class="text-sm">{{ address }}</p></div></footer>
    </div>
  </div>
</template>