<script>
	import Korben from "./Korben.svelte";
  </script>
  <main>
	<Korben card_desc="description" card_title="Articles Korben" />
  </main>
  <style>
	main {
	  text-align: center;
	  padding: 1em;
	  max-width: 240px;
	  margin: 0 auto;
	}
	h1 {
	  color: #FF3E00;
	  text-transform: uppercase;
	  font-size: 4em;
	  font-weight: 100;
	}
	@media (min-width: 640px) {
	  main {
		max-width: none;
	  }
	}
  </style>