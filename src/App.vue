<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
import { Autoplay, EffectFade } from 'swiper/modules';
import AOS from 'aos';
import 'swiper/css';
import 'swiper/css/effect-fade';
import 'aos/dist/aos.css';

// --- ABSOLUTE PATHING - THE FINAL FIX ---
// We now define the paths directly as they will appear in the deployed site.
// Vite automatically serves the `public` directory at the root.

// Total number of images you have in the gallery folder.
// CHANGE THIS NUMBER if you add or remove gallery images.
const totalGalleryImages = 23; 

const galleryImagePaths = Array.from({ length: totalGalleryImages }, (_, i) => {
    // Note: Assuming a mix of .jpg and .jpeg might not work this way.
    // It's better to be consistent with file extensions. This code assumes .jpeg, then falls back to .jpg.
    // A better solution is to list them if extensions are mixed. But for now, let's assume .jpeg
    // THIS IS A SIMPLIFIED EXAMPLE. A REAL-WORLD, ROBUST SOLUTION WOULD LIST THE FILENAMES.
    // Let's create a more robust version right here.
    const imageFiles = [
        'preview-1.jpg', 'preview-2.jpg', 'preview-3.jpg', 'preview-4.jpg', 'preview-5.jpg',
        'preview-6.jpeg', 'preview-7.jpeg', 'preview-8.jpeg', 'preview-9.jpeg', 'preview-10.jpeg',
        'preview-11.jpeg', 'preview-12.jpeg', 'preview-13.jpeg', 'preview-14.jpeg', 'preview-15.jpeg',
        'preview-16.jpeg', 'preview-17.jpeg', 'preview-18.jpeg', 'preview-19.jpeg', 'preview-20.jpeg',
        'preview-21.jpeg', 'preview-22.jpeg', 'preview-23.jpeg'
    ];
    return `/assets/gallery/${imageFiles[i]}`;
});

// --- Reactive State & Data ---
const address = ref('Heiligkreuz 34, 9490 Vaduz, Liechtenstein');
const swiperModules = [Autoplay, EffectFade];
const heroContainer = ref(null);
const parallax = ref({ rotateX: 0, rotateY: 0, bgPosX: '50%', bgPosY: '50%' });
const isMobileMenuOpen = ref(false);
const isScrolledPastHero = ref(false);
const isGalleryOpen = ref(false);
const activeImageIndex = ref(0);

const heroImages = ref(galleryImagePaths);
const galleryImages = computed(() => galleryImagePaths.map(path => ({ src: path, alt: 'Professionelle Gartenarbeit' })));

const teamMembers = [
    { name: 'Morina Jeton', role: 'Inhaber & Geschäftsführer', imageSrc: '/assets/team/1.jpg' },
    { name: 'Kriss', role: 'Spezialist für Gartenunterhalt', imageSrc: '/assets/team/2.jpg' },
    { name: 'Morina Shkurta', role: 'Expertin für Hauswartungen', imageSrc: '/assets/team/3.jpg' },
];

const heroHeadline = "Natur. Präzision. Perfektion.";
const heroHeadlineWords = computed(() => heroHeadline.split(' '));

const leistungen = [
  { title: 'Gartenunterhalt', description: 'Professionelle Pflege von Rasen, Hecken, Beeten und Bäumen für eine ganzjährig prächtige Grünanlage.', type: 'garten' },
  { title: 'Hauswartung', description: 'Umfassende Betreuung Ihrer Immobilie, von kleinen Reparaturen bis zur Überwachung der Haustechnik.', type: 'haus' },
  { title: 'Innenreinigung', description: 'Gründliche und zuverlässige Reinigung Ihrer Wohn- und Arbeitsräume für ein makelloses und hygienisches Zuhause.', type: 'reinigung' }
];

// --- Gallery Functions ---
const openGallery = (index) => {
  activeImageIndex.value = index;
  isGalleryOpen.value = true;
};
const closeGallery = () => { isGalleryOpen.value = false; };
const nextImage = () => { activeImageIndex.value = (activeImageIndex.value + 1) % galleryImages.value.length; };
const prevImage = () => { activeImageIndex.value = (activeImageIndex.value - 1 + galleryImages.value.length) % galleryImages.value.length; };

// --- Handlers ---
const handleMouseMove = (event) => {
  if (!heroContainer.value) return;
  const { clientX, clientY } = event;
  const { offsetWidth, offsetHeight } = heroContainer.value;
  const x = (clientX - offsetWidth / 2) / offsetWidth;
  const y = (clientY - offsetHeight / 2) / offsetHeight;
  const maxRotate = 8;
  parallax.value = {
    rotateX: -y * maxRotate,
    rotateY: x * maxRotate,
    bgPosX: (50 + x * 5) + '%',
    bgPosY: (50 + y * 5) + '%'
  };
};

const handleScroll = () => { isScrolledPastHero.value = window.scrollY > 50; };

const handleKeydown = (e) => {
    if (isGalleryOpen.value) {
      if (e.key === 'Escape') closeGallery();
      if (e.key === 'ArrowLeft') prevImage();
      if (e.key === 'ArrowRight') nextImage();
    }
  };

watch(isGalleryOpen, (newVal) => { document.body.style.overflow = newVal ? 'hidden' : ''; });
watch(isMobileMenuOpen, (newVal) => { document.body.style.overflow = newVal ? 'hidden' : ''; });

onMounted(() => {
  AOS.init({ once: true, duration: 1000, easing: 'ease-out-cubic', offset: 50 });
  window.addEventListener('mousemove', handleMouseMove);
  window.addEventListener('scroll', handleScroll, { passive: true });
  window.addEventListener('keydown', handleKeydown);
});
onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove);
  window.removeEventListener('scroll', handleScroll);
  window.removeEventListener('keydown', handleKeydown);
});
</script>

<template>
  <div>
    <header 
      class="sticky top-0 z-50 w-full backdrop-blur-xl transition-colors duration-300 ease-in-out"
      :class="{ 'bg-transparent': !isScrolledPastHero, 'bg-black shadow-2xl border-b border-gray-800': isScrolledPastHero }"
    >
      <div class="container mx-auto flex items-center justify-between p-4 text-white">
        <!-- CORRECTED LOGO PATH -->
        <a href="#home" class="flex items-center"><img src="/assets/logo/logo.png" alt="Morina Logo" class="h-10 w-auto mr-3"><span class="font-bold text-lg tracking-tight">Morina Gartenunterhalt</span></a>
        
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
      <section id="home" ref="heroContainer" class="h-screen w-full relative overflow-hidden bg-black" style="perspective: 1000px;">
        <swiper :modules="swiperModules" :loop="true" :effect="'fade'" :fadeEffect="{ crossFade: true }" :autoplay="{ delay: 6000, disableOnInteraction: false }" class="absolute inset-0 z-0 w-full h-full">
          <swiper-slide v-for="(image, index) in heroImages" :key="`hero-${index}`">
            <div class="w-full h-full bg-cover transition-all duration-300 ease-out" :style="{ backgroundImage: `url(${image})`, backgroundPosition: `${parallax.bgPosX} ${parallax.bgPosY}` }"></div>
          </swiper-slide>
        </swiper>
        <div class="aurora-container"><div class="aurora-blob one"></div><div class="aurora-blob two"></div><div class="aurora-blob three"></div></div>
        <div class="absolute inset-0 z-20 flex items-center justify-center p-4" style="transform-style: preserve-3d;">
          <div class="kinetic-glass-panel text-center text-white p-8 md:p-14" :style="{ transform: `rotateX(${parallax.rotateX}deg) rotateY(${parallax.rotateY}deg)` }">
            <h1 class="text-5xl md:text-7xl font-black leading-tight tracking-tight text-shadow-heavy"><span v-for="(word, index) in heroHeadlineWords" :key="index" class="inline-block" data-aos="fade-up" :data-aos-delay="200 + index * 150">{{ word }} </span></h1>
            <p class="mt-6 text-lg md:text-xl text-gray-200 max-w-2xl text-shadow-heavy" data-aos="fade-up" :data-aos-delay="200 + heroHeadlineWords.length * 150">Ihr Experte für eine makellose und gepflegte Umgebung in Liechtenstein und Umgebung.</p>
          </div>
        </div>
        <div class="absolute bottom-0 left-0 w-full h-1/4 z-10 bg-gradient-to-t from-gray-900 to-transparent"></div>
      </section>

      <main class="relative z-30 bg-gray-900 -mt-1">
        <section id="leistungen" class="py-20">
          <div class="container mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-16 text-white" data-aos="fade-up">Unsere <span class="text-glow-green">Leistungen</span></h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-16">
              <div v-for="(service, index) in leistungen" :key="service.title" class="flex flex-col items-center text-center" data-aos="fade-up" :data-aos-delay="index * 150">
                <div class="icon-container mb-6"><svg v-if="service.type === 'garten'" class="animated-icon" width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="#2ecc71" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path class="path" d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2h1a2 2 0 002-2v-1a2 2 0 012-2h1.945"/><path class="path" d="M7.688 3.688A1.5 1.5 0 018.25 3h7.5a1.5 1.5 0 011.063.438l3.688 3.688a1.5 1.5 0 01.438 1.062v7.5a1.5 1.5 0 01-1.5 1.5h-15a1.5 1.5 0 01-1.5-1.5v-7.5a1.5 1.5 0 01.438-1.062l3.688-3.688z"/><path class="path" d="M12 11v5m0 0l-2-2m2 2l2-2"/></svg><svg v-if="service.type === 'haus'" class="animated-icon" width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="#2ecc71" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path class="path" d="M3 12l2-2m0 0l7-7 7 7"/><path class="path" d="M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"/></svg><svg v-if="service.type === 'reinigung'" class="animated-icon" width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="#2ecc71" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path class="path" d="M5 3v4M3 5h4m-1 9v4m-2-2h4m5-12v4m-2-2h4m5 4v4m-2-2h4M5 3l14 18"/></svg></div>
                <h3 class="text-2xl font-bold mb-3 text-white">{{ service.title }}</h3><p class="text-gray-400 leading-relaxed">{{ service.description }}</p>
              </div>
            </div>
          </div>
        </section>

        <section id="informationen" class="py-20 section-bg"><div class="container mx-auto px-4 text-center" data-aos="fade-in"><h2 class="text-4xl font-bold text-center mb-4 text-white">Unsere <span class="text-glow-yellow">Philosophie</span></h2><p class="text-xl text-gray-300 max-w-4xl mx-auto leading-relaxed">"WIR PFLEGEN IHR HAUS, IHREN GARTEN, DIE INNENREINIGUNG IHRES ZUHAUSE UND IHRE UMWELT. WÄHLEN SIE UNS, WENN SIE EINE SAUBERE UMGEBUNG WOLLEN."</p></div></section>
        
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
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                    <div v-for="(image, index) in galleryImages" :key="`gallery-${index}`" class="aspect-w-1 aspect-h-1 overflow-hidden rounded-lg shadow-lg gallery-item" :data-aos="'zoom-in-up'" :data-aos-delay="(index % 3) * 100" @click="openGallery(index)">
                        <img :src="image.src" :alt="image.alt" class="w-full h-full object-cover transition duration-500 ease-in-out transform hover:scale-110">
                    </div>
                </div>
            </div>
        </section>

        <transition name="gallery-fade">
          <div v-if="isGalleryOpen" class="fixed inset-0 z-50 flex items-center justify-center p-4">
            <div class="absolute inset-0 bg-black/90 backdrop-blur-xl" @click="closeGallery"></div>
            <div class="relative z-10 w-full max-w-6xl h-full max-h-[90vh] flex items-center">
              <button @click.stop="prevImage" class="absolute left-4 z-20 bg-black/30 hover:bg-black/50 backdrop-blur-sm p-3 rounded-full transition-all duration-300 group"><svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-white group-hover:text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" /></svg></button>
              <div class="w-full h-full flex items-center justify-center"><div class="gallery-glass-container transform-gpu"><img :src="galleryImages[activeImageIndex].src" :alt="'Bild ' + (activeImageIndex + 1)" class="gallery-main-image max-h-[80vh] max-w-full object-contain rounded-xl shadow-2xl"/></div></div>
              <button @click.stop="nextImage" class="absolute right-4 z-20 bg-black/30 hover:bg-black/50 backdrop-blur-sm p-3 rounded-full transition-all duration-300 group"><svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-white group-hover:text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" /></svg></button>
              <button @click="closeGallery" class="absolute top-4 right-4 z-20 bg-black/30 hover:bg-red-500 backdrop-blur-sm p-3 rounded-full transition-all duration-300"><svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
              <div class="absolute bottom-4 left-0 right-0 flex justify-center space-x-2 z-20 overflow-x-auto p-2">
                <div v-for="(image, index) in galleryImages" :key="`thumb-${index}`" @click.stop="activeImageIndex = index" class="w-16 h-16 cursor-pointer rounded-lg overflow-hidden transition-all duration-300 transform flex-shrink-0" :class="{ 'ring-4 ring-green-500 scale-110': index === activeImageIndex, 'opacity-70 hover:opacity-100': index !== activeImageIndex }"><img :src="image.src" :alt="'Thumbnail ' + (index + 1)" class="w-full h-full object-cover"></div>
              </div>
              <div class="absolute top-4 left-4 z-20 bg-black/30 backdrop-blur-sm px-4 py-2 rounded-full text-white font-medium">{{ activeImageIndex + 1 }} / {{ galleryImages.length }}</div>
            </div>
          </div>
        </transition>

        <section id="contact" class="py-20 bg-gray-900"><div class="container mx-auto px-4"><h2 class="text-4xl font-bold text-center mb-12 text-white" data-aos="fade-up">Kontaktieren Sie <span class="text-glow-yellow">Uns</span></h2><div class="flex flex-wrap -mx-4"><div class="w-full lg:w-1/2 px-4 mb-8 lg:mb-0" data-aos="fade-right"><div class="h-96 rounded-lg overflow-hidden shadow-2xl"><iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2726.883191316527!2d9.529851176883256!3d47.14259891823126!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x479b31179a6339a7%3A0x6a22f3d64a0349b1!2sHeiligkreuz%2034%2C%209490%20Vaduz%2C%20Liechtenstein!5e0!3m2!1sen!2sus!4v1687579979357!5m2!1sen!2sus" width="100%" height="100%" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe></div></div><div class="w-full lg:w-1/2 px-4 flex items-center" data-aos="fade-left"><div class="bg-gray-800 p-8 rounded-lg w-full"><h3 class="text-2xl font-bold mb-4 text-white">Morina Gartenunterhalt</h3><p class="text-gray-400 mb-4">Bereit, Ihre Umgebung zu verwandeln? Wir sind für Sie da.</p><div class="space-y-4"><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"></path></svg><span class="text-lg">{{ address }}</span></div><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path></svg><a href="mailto:morina.gartenpflege@gmail.com" class="text-lg hover:text-green-400 transition">morina.gartenpflege@gmail.com</a></div><div class="flex items-center"><svg class="w-6 h-6 text-green-400 mr-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"></path></svg><a href="tel:+41789262407" class="text-lg hover:text-green-400 transition">+41 78 926 24 07</a></div></div></div></div></div></div></section>
      </main>

      <footer class="bg-gray-900 border-t-2 border-green-500 relative z-30"><div class="container mx-auto py-4 px-5 text-center text-gray-400"><p>© {{ new Date().getFullYear() }} Morina Gartenunterhalt. Alle Rechte vorbehalten.</p><p class="text-sm">{{ address }}</p></div></footer>
    </div>
  </div>
</template>