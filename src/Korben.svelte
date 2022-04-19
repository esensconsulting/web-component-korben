<svelte:options tag="korben-rss-feed" />

<script>
  export let title;
  export let direction = "column";

  let KORBE_FEED_URL = "https://korben.info/feed";
  let CORS_PROXY_URL = "https://corsanywhere.herokuapp.com/";
  const RSS_URL = CORS_PROXY_URL + KORBE_FEED_URL;

  async function getKorbenArticlesFromFeed() {
    try{
      const textResponse = await (await fetch(RSS_URL)).text();
      const data = getDataFromTextResponse(textResponse)
      return Array.from(data.querySelectorAll("item"));
    }catch(err){
       throw err
    }
       
  }
  getKorbenArticlesFromFeed();

  function getDataFromTextResponse(textResponse){
     return new window.DOMParser().parseFromString(
      textResponse,
      "text/xml"
    );
  }
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
    {#await getKorbenArticlesFromFeed()}
    <div class="spin"></div>
    {:then items}
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
      {:catch error}
          <p style="color: red">{error.message}</p>
      {/await}
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

  @keyframes spinner {
        0% {
          transform: translate3d(-50%, -50%, 0) rotate(0deg);
        }
        100% {
          transform: translate3d(-50%, -50%, 0) rotate(360deg);
        }
      }
      .spin::before {
        animation: 1.5s linear infinite spinner;
        animation-play-state: inherit;
        border: solid 5px #cfd0d1;
        border-bottom-color: #1c87c9;
        border-radius: 50%;
        content: "";
        height: 40px;
        width: 40px;
        position: absolute;
        top: 10%;
        left: 10%;
        transform: translate3d(-50%, -50%, 0);
        will-change: transform;
      }
</style>
