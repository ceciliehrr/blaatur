<template>
  <li :class="['link-card', { 'link-card--closed': closed }]">
    <a :href="href">
      <h2>
        {{ title }}
        <span>&rarr;</span>
      </h2>
      <p>
        {{ body }}
        {{ closed }}
      </p>
    </a>
  </li>
</template>

<script>
export default {
  name: "LinkCard",
  props: {
    title: {
      type: String,
      required: true,
    },
    body: {
      type: String,
      required: true,
    },
    href: {
      type: String,
      required: true,
    },
    closed: {
      type: Boolean,
      default: false,
    },
    coordinate: {
      type: Object,
      default: () => null,
      validator: (value) => {
        if (!value) return true;
        return "latitude" in value && "longitude" in value;
      },
    },
  },
};
</script>

<style scoped lang="scss">
.link-card {
  list-style: none;
  display: flex;
  padding: 1px;
  background-color: #23262d;
  border-radius: 7px;
  background-position: 100%;
  transition: background-position 0.6s cubic-bezier(0.22, 1, 0.36, 1);
  box-shadow: inset 0 0 0 1px rgba(255, 255, 255, 0.1);

  & > a {
    width: 100%;
    text-decoration: none;
    line-height: 1.4;
    padding: calc(1.5rem - 1px);
    border-radius: 8px;
    color: white;
    background-color: #23262d;
    opacity: 0.8;
  }

  h2 {
    margin: 0;
    font-size: 1.25rem;
    transition: color 0.6s cubic-bezier(0.22, 1, 0.36, 1);
  }

  p {
    margin-top: 0.5rem;
    margin-bottom: 0;
  }

  &--closed {
    opacity: 0.5;
    pointer-events: none;
  }

  span {
    margin-left: auto;
    font-size: 1.5rem;
    line-height: 1;
    transition: transform 0.6s cubic-bezier(0.22, 1, 0.36, 1);
  }
}

.link-card:is(:hover, :focus-within) {
  background-position: 0;
  background-image: var(--accent-gradient);
}

.link-card:is(:hover, :focus-within) h2 {
  color: rgb(var(--accent-light));
}
</style>
