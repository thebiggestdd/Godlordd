<h1>Vanity Gallery</h1>
<p>Capture your daily masterpieces — photo and thought included. 💭📸</p>

<div class="controls">
  <input type="file" id="imageInput" accept="image/*">
  <textarea id="textInput" placeholder="Write your vanity thought here..."></textarea>
  <button id="saveBtn">Save Card</button>
</div>

<div class="gallery" id="gallery"></div>

<script>
  const storageKey = "vanity_gallery_cards";
  let cards = JSON.parse(localStorage.getItem(storageKey)) || [];

  const gallery = document.getElementById("gallery");
  const saveBtn = document.getElementById("saveBtn");
  const imageInput = document.getElementById("imageInput");
  const textInput = document.getElementById("textInput");

  // Save new card
  saveBtn.onclick = () => {
    const file = imageInput.files[0];
    const text = textInput.value.trim();
    if (!file && !text) return alert("At least write something or add a photo, the biggest D.");

    const reader = new FileReader();
    reader.onload = function (e) {
      const imageData = file ? e.target.result : null;
      const newCard = {
        image: imageData,
        text: text,
        date: new Date().toISOString()
      };
      cards.push(newCard);
      localStorage.setItem(storageKey, JSON.stringify(cards));
      renderCards();
      imageInput.value = "";
      textInput.value = "";
    };
    if (file) reader.readAsDataURL(file);
    else reader.onload(); // For text-only cards
  };

  // Render all cards
  function renderCards() {
    gallery.innerHTML = "";
    if (cards.length === 0) {
      gallery.innerHTML = "<p style='color:#888;text-align:center;'>No vanity cards yet. Time to add your first brilliance, the biggest D of all.</p>";
      return;
    }
    cards.slice().reverse().forEach((card, i) => {
      const div = document.createElement("div");
      div.className = "card";
      div.innerHTML = `
        ${card.image ? `<img src="${card.image}" alt="Vanity Image">` : ""}
        <div class="card-content">
          <p>${escapeHtml(card.text)}</p>
          <div class="date">${new Date(card.date).toLocaleString()}</div>
          <button class="delete-btn" data-index="${cards.length - 1 - i}">Delete</button>
        </div>
      `;
      gallery.appendChild(div);
    });

    document.querySelectorAll(".delete-btn").forEach(btn => {
      btn.onclick = () => {
        const idx = btn.dataset.index;
        if (confirm("Delete this card?")) {
          cards.splice(idx, 1);
          localStorage.setItem(storageKey, JSON.stringify(cards));
          renderCards();
        }
      };
    });
  }

  function escapeHtml(text) {
    return text.replace(/[&<>"']/g, m => ({
      '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;'
    }[m]));
  }

  renderCards();
</script>

</body>
</html>

