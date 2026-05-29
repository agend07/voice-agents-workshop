<script lang="ts">
  interface Props {
    name: string;
    role: string;
    photo: string;
  }

  let { name, role, photo }: Props = $props();
</script>

<div class="flex flex-col items-center text-center gap-5 py-6">
  <img
    src={photo}
    alt={name}
    class="w-28 h-28 rounded-full ring-4 ring-orange-100 object-cover"
  />
  <div>
    <h3 class="text-2xl font-bold text-neutral-900">{name}</h3>
    <p class="text-neutral-500 mt-1">{role}</p>
  </div>
</div>
