<script>
    const email = "syyin@uchicago, syin94619@gmail.com";
  const phone = "+1 5307616910";
</script>

<h1>Contact</h1>
<p class="subtitle">
  Feel free to contact me!
</p>

<section class="info-grid">
  <div class="info-item">
    <span class="label">Email</span>
    <span>{email}</span>
  </div>
  <div class="info-item">
    <span class="label">Phone</span>
    <span>{phone}</span>
  </div>
  </section>

<style>
  h1 {
    margin-bottom: 0.25rem;
  }

  .subtitle {
    color: #666;
    margin-bottom: 2rem;
  }
    .info-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    margin-bottom: 2rem;
    padding: 1.25rem;
    background: white;
    border: 1px solid #ddd;
    border-radius: 6px;
  }

  .info-item {
    flex: 1;
    min-width: 150px;
  }
  .label {
    display: block;
    font-size: 0.8rem;
    text-transform: uppercase;
    color: #888;
    margin-bottom: 0.2rem;
  }
  </style>