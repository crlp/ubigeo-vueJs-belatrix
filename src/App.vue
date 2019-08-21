<template>
	<div id="app">
		<text-reader @load="text = $event"></text-reader>
	</div>
</template>

<script>
import TextReader from "./TextReader";

export default {
  name: "app",
  data: () => ({ text: "" }),
  components: {
    TextReader
  }
};
</script>
