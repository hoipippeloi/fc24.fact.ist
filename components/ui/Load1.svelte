<div class="loading-animation uk-container">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
  </div>

<style>
  .loading-animation { /*ok*/
    margin-top:10%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .loading-animation div {
    display: inline-block;
    width: 20px;
    height: 20px;
    margin: 0 5px;
    border-radius: 50%;
    background-color: #BC562B; /* Customize the color here */
    animation: loading 1.5s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  }
  
  .loading-animation div:nth-of-type(2) {
    animation-delay: 0.1s;
  }
  
  .loading-animation div:nth-of-type(3) {
    animation-delay: 0.2s;
  }
  
  .loading-animation div:nth-of-type(4) {
    animation-delay: 0.3s;
  }

  @keyframes loading {
    0% {
      transform: scale(0);
      opacity: 0.7;
    }
    50% {
      transform: scale(1);
      opacity: 1;
    }
    100% {
      transform: scale(0);
      opacity: 0.7;
    }
  }
</style>