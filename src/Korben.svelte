<svelte:options tag="korben-rss-feed" />
<script>
  export let title;
 
  let CORS_PROXY_URL = 'https://corsanywhere.herokuapp.com/'
  let KORBE_FEED_URL= 'https://korben.info/feed'
  const RSS_URL = CORS_PROXY_URL + KORBE_FEED_URL;
  let items = [];
 
  async function getKorbenArticlesFromFeed() {
       const textResponse = await (await fetch(RSS_URL,{
        method:'GET',
      })).text();
    const data = new window.DOMParser().parseFromString(
      textResponse,
      "text/xml"
    );
    items = Array.from(data.querySelectorAll("item"));
    console.log("items", items[0].getElementsByTagName("link")[0].innerHTML);
    return items;
  }
 items = getKorbenArticlesFromFeed();
</script>

<main>
  <div class="card-container">
    <div class="card">
      <div class="card-body">
        <div class="row">
          <div class="card-title">
            <h2>{title}</h2>
          </div>
        </div>
        {#if items && items.length > 0}
          {#each items as item}
           <a target="_blank"  href={item.getElementsByTagName("link")[0].innerHTML}>{item.getElementsByTagName("title")[0].innerHTML}</a>
            <br />
          {/each}
        {/if}
      </div>
    </div>
  </div>
</main>

<style>
  .card {
    max-width: 500px;
    border-radius: 5px;
    box-shadow: 0 4px 6px 0 #00000033;
    padding: 0 0 10px 0;
  }
  .card-body {
    padding: 5px 10px;
  }

</style>