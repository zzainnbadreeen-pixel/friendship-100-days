const backgroundLayer = document.getElementById('backgroundLayer');
const countNumber = document.getElementById('countNumber');
const countBtn = document.getElementById('countBtn');
const counterScreen = document.getElementById('counterScreen');
const letterScreen = document.getElementById('letterScreen');
const messageScreen = document.getElementById('messageScreen');
const envelope = document.getElementById('envelope');
const openLetterBtn = document.getElementById('openLetterBtn');
const songFrame = document.getElementById('songFrame');

const emojis = ['😍', '💕', '🌹', '💖', '✨', '💌'];

function createBackgroundBubbles() {
  for (let i = 0; i < 24; i++) {
    const bubble = document.createElement('div');
    bubble.className = 'floating-emoji';
    bubble.textContent = emojis[i % emojis.length];

    const startX = Math.random() * 120 - 10;
    const startY = Math.random() * 100;
    const midX = Math.random() * 55 + 10;
    const midY = Math.random() * 40 + 10;
    const endX = Math.random() * 120 + 10;
    const endY = Math.random() * 100 + 30;

    bubble.style.setProperty('--start-x', `${startX}vw`);
    bubble.style.setProperty('--start-y', `${startY}vh`);
    bubble.style.setProperty('--mid-x', `${midX}vw`);
    bubble.style.setProperty('--mid-y', `${midY}vh`);
    bubble.style.setProperty('--end-x', `${endX}vw`);
    bubble.style.setProperty('--end-y', `${endY}vh`);
    bubble.style.setProperty('--float-duration', `${12 + Math.random() * 12}s`);
    bubble.style.setProperty('--delay', `${(Math.random() * 6).toFixed(1)}s`);

    bubble.style.left = '0';
    bubble.style.top = '0';
    backgroundLayer.appendChild(bubble);
  }
}

function animateCounter() {
  let start = 0;
  const end = 100;
  const duration = 10000;
  const startTime = performance.now();

  function tick(now) {
    const progress = Math.min((now - startTime) / duration, 1);
    const eased = 1 - Math.pow(1 - progress, 3);
    const value = Math.round(start + (end - start) * eased);
    countNumber.textContent = value;

    if (progress < 1) {
      requestAnimationFrame(tick);
    } else {
      countNumber.textContent = '100';
      setTimeout(() => {
        counterScreen.classList.remove('active');
        letterScreen.classList.add('active');
      }, 320);
    }
  }

  requestAnimationFrame(tick);
}

countBtn.addEventListener('click', () => {
  countBtn.disabled = true;
  countBtn.style.opacity = '0.7';
  animateCounter();
});

let dragStartY = 0;
let dragCurrentY = 0;
let isDragging = false;
let opened = false;

function revealLetter() {
  if (opened) return;
  opened = true;
  envelope.classList.add('opened');
  setTimeout(() => {
    openLetterBtn.classList.remove('hidden');
  }, 500);
}

envelope.addEventListener('pointerdown', (event) => {
  if (opened) return;
  isDragging = true;
  dragStartY = event.clientY;
  dragCurrentY = event.clientY;
  envelope.classList.add('dragging');
  envelope.setPointerCapture(event.pointerId);
});

envelope.addEventListener('pointermove', (event) => {
  if (!isDragging || opened) return;
  dragCurrentY = event.clientY;
  const delta = dragStartY - dragCurrentY;

  if (delta > 30) {
    revealLetter();
  }
});

envelope.addEventListener('pointerup', () => {
  isDragging = false;
  envelope.classList.remove('dragging');
});

envelope.addEventListener('pointerleave', () => {
  if (!opened) {
    isDragging = false;
    envelope.classList.remove('dragging');
  }
});

openLetterBtn.addEventListener('click', () => {
  letterScreen.classList.remove('active');
  messageScreen.classList.add('active');
  songFrame.src = 'https://www.youtube.com/embed/AX6fiTtQqRI?autoplay=1&mute=1&loop=1&playlist=AX6fiTtQqRI';
});

createBackgroundBubbles();
