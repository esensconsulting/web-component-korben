<svelte:options tag="korben-rss-feed" />

<script>
  export let title;

  let KORBE_FEED_URL = "https://korben.info/feed";
  let CORS_PROXY_URL = "https://corsanywhere.herokuapp.com/";
  const RSS_URL = CORS_PROXY_URL + KORBE_FEED_URL;
  let items = [];

  async function getKorbenArticlesFromFeed() {
    const textResponse = await (
      await fetch(RSS_URL, {
        method: "GET",
      })
    ).text();
    const data = new window.DOMParser().parseFromString(
      textResponse,
      "text/xml"
    );
    items = Array.from(data.querySelectorAll("item"));
  }
  getKorbenArticlesFromFeed();

  function openInNewTab(url) {
    window.open(url, "_blank").focus();
  }
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
            <div
              class="article"
              style="display: flex; align-items: center;"
              target="_blank"
              on:click={openInNewTab(item.getElementsByTagName("link")[0].innerText)}
            >
              <img
                class="image"
                style=""
                src={item
                  .getElementsByTagName("media:content")[0]
                  .getAttribute("url")}
                height={item
                  .getElementsByTagName("media:content")[0]
                  .getAttribute("height")}
                width={item
                  .getElementsByTagName("media:content")[0]
                  .getAttribute("width")}
                alt={item.getElementsByTagName("title")[0].innerHTML}
              />

              <div class="details">
                <div class="title">
                  {item.getElementsByTagName("title")[0].innerHTML}
                </div>
                <div class="description">
                  {item.getElementsByTagName("description")[0].innerHTML}
                </div>
              </div>
            </div>
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

  .article{}
</style>
