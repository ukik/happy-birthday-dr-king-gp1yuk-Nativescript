<page class="bg-black" on:loaded={loadedPage} on:unloaded={unloadedPage}>
  <gridLayout>
    <contentView on:loaded={loadedTwo}>
      <imageCacheIt src={currentImageTwo} stretch="aspectFill" />
    </contentView>
    <contentView on:loaded={loadedOne}>
      <imageCacheIt src={currentImageOne} stretch="aspectFill" />
    </contentView>
    <gridLayout rows="*,auto,auto,*" on:tap={openWikipedia} class="align-bottom bg-black/50" height="120">
        <label row="1" text="Happy Birthday Dr. King"  class="align-middle text-2xl mb-2 text-center text-white" />
        <label row="2" text="⚖️➕👐"  class="align-middle text-3xl text-center text-white" />
        
    </gridLayout>
  </gridLayout>
</page>

<script lang="ts">
  import { CoreTypes, Utils, Animation } from '@nativescript/core';
  import { ImageCacheIt } from '@triniwiz/nativescript-image-cache-it';

  let images = [
    'https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Martin_Luther_King_press_conference_01269u_edit.jpg/1200px-Martin_Luther_King_press_conference_01269u_edit.jpg',
    'https://upload.wikimedia.org/wikipedia/commons/8/84/Martin_Luther_King_Jr_NYWTS.jpg',
    'https://img.washingtonpost.com/rf/image_480w/2010-2019/WashingtonPost/2013/08/14/National-Enterprise/Images/32285111376502121.jpg?uuid=1YnxlAUIEeO_xUBrkoYDsg',
    'https://i.pinimg.com/originals/5c/72/af/5c72af49bca6d048a98e5a139d9491d9.jpg',
    'https://m.media-amazon.com/images/I/61s7v5YIVcL._AC_SL1471_.jpg',
    'https://d1csarkz8obe9u.cloudfront.net/posterpreviews/martin-luther-king-jr-quotes-love...-design-template-449bb934cc24daddb0a6fa1557aaa2cd_screen.jpg?ts=1579405806',
    'https://thinkkindness.org/wp-content/uploads/2020/06/martin-luther-king-quotes-thinkkindness.com-1.jpg',
    'https://i.guim.co.uk/img/media/c1752678b87f1ae6eee6a635aa2844fdbf58fdb2/0_588_1984_1190/master/1984.jpg?width=620&quality=45&dpr=2&s=none',
    'https://media.vaticannews.va/media/content/dam-archive/vaticannews/multimedia/2018/10/2/martin-luther-king.jpg/_jcr_content/renditions/cq5dam.thumbnail.cropped.1500.844.jpeg',
  ];
  let currentImageOneIndex = 0;
  let currentImageOne = images[currentImageOneIndex];
  let currentImageTwoIndex = 1;
  let currentImageTwo = images[currentImageTwoIndex];
  let stopAnimationsCount = 0;
  let loadedOneTimeout;
  let loadedTwoTimeout;

  function loadedPage(args) {
    args.object.actionBarHidden = true;
  }

  function unloadedPage(args) {
    // after a page unload we want to stop the next 2 animations
    stopAnimationsCount = 2;
  }
  
  function loadedOne(args) {
    if (typeof loadedOneTimeout === 'number') {
      clearTimeout(loadedOneTimeout);
    }
    loadedOneTimeout = setTimeout(() => {
      startAnimateOne(<ImageCacheIt>args.object);
    }, 1000);
  }

  function loadedTwo(args) {
    if (typeof loadedTwoTimeout === 'number') {
      clearTimeout(loadedTwoTimeout);
    }
    loadedTwoTimeout = setTimeout(() => {
      startAnimateTwo(<ImageCacheIt>args.object);
    }, 1000);
  }

  function startAnimateOne(image: ImageCacheIt) {
    image
      .animate({
        scale: { x: 2, y: 2 },
        duration: 7000,
        curve: CoreTypes.AnimationCurve.easeInOut,
      })
      .then(() => {})
      .catch(() => {});

    image
      .animate({
        opacity: 0,
        delay: 5000,
        duration: 2000,
        curve: CoreTypes.AnimationCurve.easeInOut,
      })
      .then(() => {
        startAnimateTwo(image, 1500, true, true);
      })
      .catch(() => {});
  }

  function startAnimateTwo(
    image: ImageCacheIt,
    delay = 5000,
    skipScale = false,
    switchOne = false
  ) {
    if (stopAnimationsCount) {
      stopAnimationsCount--;

      return;
    }

    image.opacity = 0;
    if (switchOne) {
      currentImageOneIndex = currentImageOneIndex + 2;
      if (currentImageOneIndex > images.length) {
        currentImageOneIndex = 0;
      }
      currentImageOne = images[currentImageOneIndex];
      image.scaleX = image.scaleY = 1;
    }
    image
      .animate({
        opacity: 1,
        delay,
        duration: 2000,
        curve: CoreTypes.AnimationCurve.easeInOut,
      })
      .then(() => {
        image
          .animate({
            opacity: 0,
            delay: 1000,
            duration: 2000,
            curve: CoreTypes.AnimationCurve.easeInOut,
          })
          .then(() => {
            if (!switchOne) {
              currentImageTwoIndex = currentImageTwoIndex + 2;
              if (currentImageTwoIndex > images.length) {
                currentImageTwoIndex = 1;
              }
              currentImageTwo = images[currentImageTwoIndex];
              image.scaleX = image.scaleY = 1;
            }
            startAnimateTwo(image, 2000, true, switchOne);
          })
          .catch(() => {});
      })
      .catch(() => {});
    image
      .animate({
        scale: { x: 2, y: 2 },
        delay,
        duration: 7000,
        curve: CoreTypes.AnimationCurve.easeInOut,
      })
      .then(() => {})
      .catch(() => {});
  }

  function openWikipedia() {
    Utils.openUrl('https://en.wikipedia.org/wiki/Martin_Luther_King_Jr._Day');
  }
</script>
