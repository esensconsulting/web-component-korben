<svelte:options tag="korben-rss-feed" />

<script>
  export let title;
  export let direction = "column";

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

  function articleTitle(item) {
    return item.getElementsByTagName("title")[0].innerHTML;
  }

  function articleImage(item) {
    return item.getElementsByTagName("media:content")[0];
  }

  function articleImageUrl(item) {
    return articleImage(item).getAttribute("url");
  }

  function articleImageHeight(item) {
    return articleImage(item).getAttribute("height");
  }

  function articleImageWidth(item) {
    return articleImage(item).getAttribute("width");
  }

  function articleDescription(item) {
    return item
      .getElementsByTagName("description")[0]
      .innerHTML.replace(/^<\!\[CDATA\[|\]\]>$/g, "")
      .replace(/href/g, "target=_blank href");
  }
</script>

<main class="container">
  {#if title}
    <div class="title">
      <h2>{title}</h2>
    </div>
  {/if}
  <div class="articles" style="flex-direction: {direction}">
    {#if items && items.length > 0}
      {#each items as item}
        <div class="article">
          <img
            class="image"
            src={articleImageUrl(item)}
            height={articleImageHeight(item)}
            width={articleImageWidth(item)}
            alt={articleTitle(item)}
          />

          <div class="details">
            <div class="title">
              {articleTitle(item)}
            </div>
            <div class="description">
              {@html articleDescription(item)}
            </div>
          </div>
        </div>
      {/each}
    {/if}
  </div>
</main>

<style>

  .details {
    padding: 0 10px;
    min-width: 400px;
  }

  .articles {
    display: flex;
    overflow: auto;
  }
  
  .article {
    max-height: 150px;
    margin: 10px;
    max-width: 575px;
    border-radius: 5px;
    box-shadow: 0 4px 6px 0 #00000033;
    display: flex; 
    align-items: center;
  }

  .image {
    border-radius: 5px 0 0 5px;
  }

  .description {
    overflow: hidden;
    text-overflow: ellipsis;
    font-size: 14px;
    color: gray;
    font-weight: 300;
    margin: 10px 0;
  }
</style>
