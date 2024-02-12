<template>
  <Ucard>
    <template #header> Signing in... </template>
    Wait a moment while we sign you in...
  </Ucard>
</template>
<script setup>
    useRedirectIfAuthenticated()
</script>
