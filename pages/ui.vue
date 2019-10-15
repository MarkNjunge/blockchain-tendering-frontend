<template>
  <div class="container mx-auto">
    <a class="flex mt-8 up-navigation" href="#">
      <svg viewBox="0 0 320 512" class="up-navigation-icon">
        <path
          fill="currentColor"
          d="M34.52 239.03L228.87 44.69c9.37-9.37 24.57-9.37 33.94 0l22.67 22.67c9.36 9.36 9.37 24.52.04 33.9L131.49 256l154.02 154.75c9.34 9.38 9.32 24.54-.04 33.9l-22.67 22.67c-9.37 9.37-24.57 9.37-33.94 0L34.52 272.97c-9.37-9.37-9.37-24.57 0-33.94z"
        />
      </svg>
      <p class="up-navigation-text">BACK</p>
    </a>

    <h2 class="mt-4 section-title">Fill in the form below</h2>

    <div class="mt-2 card">
      <div class="input-group">
        <label for="name" class="input-label">Name</label>
        <input type="text" class="input-field" value="John Smith" />
        <span class="input-helper">Lorem ipsum dolor sit amet.</span>
      </div>

      <div class="mt-2 input-group">
        <label for="name" class="input-label">Disabled Field</label>
        <input type="text" class="input-field-disabled" value="This field is disabled" disabled />
      </div>
      <button class="mt-4 btn">Download</button>
    </div>
  </div>
</template>