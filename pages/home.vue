<template>
    <div>
        <h1>
            Hola Mundo
        </h1>
    </div>
</template>
<script>
export default {
  middleware: 'authenticated',
  head:{
    title: "Home"
  }
}
</script>