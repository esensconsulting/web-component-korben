<svelte:options tag="korben-rss-feed" />

<script>
  export let title;
  export let direction = "column";
  let selected = 5;

  let KORBE_FEED_URL = "https://korben.info/feed";
  let CORS_PROXY_URL = "https://esens-cors-proxy.herokuapp.com/";
  const RSS_URL = CORS_PROXY_URL + KORBE_FEED_URL;

  async function getKorbenArticlesFromFeed() {
    try {
      const textResponse = await (await fetch(RSS_URL)).text();
      const data = getDataFromTextResponse(textResponse);
      return Array.from(data.querySelectorAll("item"));
    } catch (err) {
      throw err;
    }
  }
  getKorbenArticlesFromFeed();

  function getDataFromTextResponse(textResponse) {
    return new window.DOMParser().parseFromString(textResponse, "text/xml");
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
  <div class="articles selected-{selected}" style="flex-direction: {direction}; ">
    {#await getKorbenArticlesFromFeed()}
      <div class="spin" />
    {:then items}
      {#if items && items.length > 0}
        {#each items as item, i}
          <label class="article" for="article-{i + 1}" class:selected={selected == i + 1}>
            <input
              checked={ selected === i + 1}
              class="radio"
              id="article-{i + 1}"
              type="radio"
              bind:group={selected}
              name="position"
              value="{i + 1}"
            />
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
          </label>
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

  input {
    display: none;
  }

  .articles {
    width: 100vw;
    height: 170px;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    transform-style: preserve-3d;
    perspective: 500px;
    --items: 10;
    --position: 5;
    --middle: 5;
  }

  .article {
    height: 150px;
    margin: 10px;
    width: 650px;
    background-color: white;
    border-radius: 5px;
    box-shadow: 0 4px 6px 0 #00000033;
    display: flex; 
    align-items: center;
    position: absolute;
    --r: calc(var(--position) - var(--offset));
    --abs: max(calc(var(--r) * -1), var(--r));
    transition: all 0.25s linear;
    transform: rotateY(calc(-10deg * var(--r)))
      translateX(calc(-300px * var(--r)));
    z-index: calc((var(--position) - var(--abs)));
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

  .article:nth-of-type(1) {
    --offset: 1;
  }
  .article:nth-of-type(2) {
    --offset: 2;
  }
  .article:nth-of-type(3) {
    --offset: 3;
  }
  .article:nth-of-type(4) {
    --offset: 4;
  }
  .article:nth-of-type(5) {
    --offset: 5;
  }

  .article:nth-of-type(6) {
    --offset: 6;
  }

  .article:nth-of-type(7) {
    --offset: 7;
  }

  .article:nth-of-type(8) {
    --offset: 8;
  }

  .article:nth-of-type(9) {
    --offset: 9;
  }

  .article:nth-of-type(10) {
    --offset: 10;
  }
  input:nth-of-type(1) {
    grid-column: 2 / 3;
    grid-row: 2 / 3;
  }
  .selected-1 {
    --position: 1;
  }

  input:nth-of-type(2) {
    grid-column: 3 / 4;
    grid-row: 2 / 3;
  }
  .selected-2  {
    --position: 2;
  }

  input:nth-of-type(3) {
    grid-column: 4 /5;
    grid-row: 2 / 3;
  }
  .selected-3  {
    --position: 3;
  }

  input:nth-of-type(4) {
    grid-column: 5 / 6;
    grid-row: 2 / 3;
  }
  .selected-4  {
    --position: 4;
  }

  input:nth-of-type(5) {
    grid-column: 6 / 7;
    grid-row: 2 / 3;
  }
  .selected-5  {
    --position: 5;
  }

  input:nth-of-type(6) {
    grid-column: 7 / 8;
    grid-row: 2 / 3;
  }

  .selected-6 {
    --position: 6;
  }

  input:nth-of-type(7) {
    grid-column: 8 / 9;
    grid-row: 2 / 3;
  }

  .selected-7 {
    --position: 7;
  }

  input:nth-of-type(8) {
    grid-column: 9 / 10;
    grid-row: 2 / 3;
  }

  .selected-8  {
    --position: 8;
  }

  input:nth-of-type(9) {
    grid-column: 10 / 11;
    grid-row: 2 / 3;
  }

  .selected-9  {
    --position: 9;
  }
  
  input:nth-of-type(10) {
    grid-column: 11 / 12;
    grid-row: 2 / 3;
  }
  
  .selected-10  {
    --position: 10;
  }
</style>
