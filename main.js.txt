(() => {
  const toggle = document.querySelector('.menu-toggle');
  const nav = document.querySelector('.nav-links');
  if (toggle && nav) {
    toggle.addEventListener('click', () => {
      const open = nav.classList.toggle('open');
      document.body.classList.toggle('menu-open', open);
      toggle.setAttribute('aria-expanded', String(open));
      toggle.setAttribute('aria-label', open ? 'Close navigation menu' : 'Open navigation menu');
      toggle.textContent = open ? '×' : '☰';
    });
    nav.querySelectorAll('a').forEach(link => link.addEventListener('click', () => {
      nav.classList.remove('open'); document.body.classList.remove('menu-open');
      toggle.setAttribute('aria-expanded', 'false'); toggle.textContent = '☰';
    }));
  }
  const current = location.pathname.split('/').pop() || 'index.html';
  document.querySelectorAll('.nav-links a').forEach(a => {
    if (a.getAttribute('href') === current) a.classList.add('active');
  });
  const revealEls = document.querySelectorAll('.reveal');
  if ('IntersectionObserver' in window && !window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    const observer = new IntersectionObserver(entries => entries.forEach(entry => {
      if (entry.isIntersecting) { entry.target.classList.add('visible'); observer.unobserve(entry.target); }
    }), {threshold: .12});
    revealEls.forEach(el => observer.observe(el));
  } else revealEls.forEach(el => el.classList.add('visible'));

  const bookingForm = document.querySelector('#booking-form');
  if (bookingForm) {
    const requestedService = new URLSearchParams(location.search).get('service');
    const serviceSelect = bookingForm.querySelector('[name="service"]');
    if (requestedService && serviceSelect) {
      const match = [...serviceSelect.options].find(option => option.text.trim().toLowerCase() === requestedService.trim().toLowerCase());
      if (match) serviceSelect.value = match.value;
    }
    const date = bookingForm.querySelector('[name="date"]');
    if (date) { const now = new Date(); date.min = new Date(now.getTime() - now.getTimezoneOffset() * 60000).toISOString().slice(0,10); }
    bookingForm.addEventListener('submit', e => {
      e.preventDefault();
      if (!bookingForm.reportValidity()) return;
      const data = new FormData(bookingForm);
      const fields = [
        ['Name', data.get('name')], ['Phone', data.get('phone')], ['Service', data.get('service')],
        ['Preferred date', data.get('date') || 'Flexible'], ['Preferred time', data.get('time') || 'Flexible'],
        ['Additional request', data.get('message') || 'None']
      ];
      const message = 'Hello NANDANI WELLNESS SPA, I would like to enquire about an appointment.\n\n' + fields.map(([k,v]) => `${k}: ${v}`).join('\n') + '\n\nPlease confirm availability and any applicable charges.';
      const url = 'https://wa.me/919156361327?text=' + encodeURIComponent(message);
      const msg = document.querySelector('#booking-message');
      if (msg) { msg.textContent = 'Your request is ready. WhatsApp will open so you can send it to the spa. Your appointment is not confirmed until the spa replies.'; msg.classList.add('show'); }
      window.open(url, '_blank', 'noopener,noreferrer');
    });
  }

  const lightbox = document.querySelector('.lightbox');
  if (lightbox) {
    const big = lightbox.querySelector('img');
    const close = () => { lightbox.classList.remove('open'); lightbox.setAttribute('aria-hidden','true'); document.body.style.overflow = ''; };
    document.querySelectorAll('[data-gallery-image]').forEach(btn => btn.addEventListener('click', () => {
      const img = btn.querySelector('img'); if (!img) return;
      big.src = img.currentSrc || img.src; big.alt = img.alt; lightbox.classList.add('open'); lightbox.setAttribute('aria-hidden','false'); document.body.style.overflow='hidden';
    }));
    lightbox.querySelector('.lightbox-close').addEventListener('click', close);
    lightbox.addEventListener('click', e => { if (e.target === lightbox) close(); });
    document.addEventListener('keydown', e => { if (e.key === 'Escape') close(); });
  }
  document.querySelectorAll('[data-year]').forEach(el => el.textContent = new Date().getFullYear());
})();
