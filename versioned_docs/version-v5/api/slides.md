---
title: 'Ion-Slides: Mobile Touch Slider with Built-In & Custom Animation'
description: 'Ion-Slides is a multi-section container which offers custom and built-in mobile touch slider animation effects. See how Ion-Slides works with iOS and Android.'
sidebar_label: 'ion-slides'
demoUrl: '/docs/demos/api/slides/index.html'
demoSourceUrl: 'https://github.com/ionic-team/ionic-docs/tree/main/static/demos/api/slides/index.html'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# ion-slides

The Slides component is a multi-section container. Each section can be swiped
or dragged between. It contains any number of [Slide](slide.md) components.

Adopted from Swiper.js:
The most modern mobile touch slider and framework with hardware accelerated transitions.

http://www.idangero.us/swiper/

Copyright 2016, Vladimir Kharlampidi
The iDangero.us
http://www.idangero.us/

Licensed under MIT

## Custom Animations

By default, Ionic slides use the built-in `slide` animation effect. Custom animations can be provided via the `options` property. Examples of other animations can be found below.

### Coverflow

```tsx
const slideOpts = {
  slidesPerView: 3,
  coverflowEffect: {
    rotate: 50,
    stretch: 0,
    depth: 100,
    modifier: 1,
    slideShadows: true,
  },
  on: {
    beforeInit() {
      const swiper = this;

      swiper.classNames.push(
        `${swiper.params.containerModifierClass}coverflow`,
      );
      swiper.classNames.push(`${swiper.params.containerModifierClass}3d`);

      swiper.params.watchSlidesProgress = true;
      swiper.originalParams.watchSlidesProgress = true;
    },
    setTranslate() {
      const swiper = this;
      const {
        width: swiperWidth,
        height: swiperHeight,
        slides,
        $wrapperEl,
        slidesSizesGrid,
        $,
      } = swiper;
      const params = swiper.params.coverflowEffect;
      const isHorizontal = swiper.isHorizontal();
      const transform$$1 = swiper.translate;
      const center = isHorizontal
        ? -transform$$1 + swiperWidth / 2
        : -transform$$1 + swiperHeight / 2;
      const rotate = isHorizontal ? params.rotate : -params.rotate;
      const translate = params.depth;
      // Each slide offset from center
      for (let i = 0, length = slides.length; i < length; i += 1) {
        const $slideEl = slides.eq(i);
        const slideSize = slidesSizesGrid[i];
        const slideOffset = $slideEl[0].swiperSlideOffset;
        const offsetMultiplier =
          ((center - slideOffset - slideSize / 2) / slideSize) *
          params.modifier;

        let rotateY = isHorizontal ? rotate * offsetMultiplier : 0;
        let rotateX = isHorizontal ? 0 : rotate * offsetMultiplier;
        // var rotateZ = 0
        let translateZ = -translate * Math.abs(offsetMultiplier);

        let translateY = isHorizontal ? 0 : params.stretch * offsetMultiplier;
        let translateX = isHorizontal ? params.stretch * offsetMultiplier : 0;

        // Fix for ultra small values
        if (Math.abs(translateX) < 0.001) translateX = 0;
        if (Math.abs(translateY) < 0.001) translateY = 0;
        if (Math.abs(translateZ) < 0.001) translateZ = 0;
        if (Math.abs(rotateY) < 0.001) rotateY = 0;
        if (Math.abs(rotateX) < 0.001) rotateX = 0;

        const slideTransform = `translate3d(${translateX}px,${translateY}px,${translateZ}px)  rotateX(${rotateX}deg) rotateY(${rotateY}deg)`;

        $slideEl.transform(slideTransform);
        $slideEl[0].style.zIndex = -Math.abs(Math.round(offsetMultiplier)) + 1;
        if (params.slideShadows) {
          // Set shadows
          let $shadowBeforeEl = isHorizontal
            ? $slideEl.find('.swiper-slide-shadow-left')
            : $slideEl.find('.swiper-slide-shadow-top');
          let $shadowAfterEl = isHorizontal
            ? $slideEl.find('.swiper-slide-shadow-right')
            : $slideEl.find('.swiper-slide-shadow-bottom');
          if ($shadowBeforeEl.length === 0) {
            $shadowBeforeEl = swiper.$(
              `<div class="swiper-slide-shadow-${
                isHorizontal ? 'left' : 'top'
              }"></div>`,
            );
            $slideEl.append($shadowBeforeEl);
          }
          if ($shadowAfterEl.length === 0) {
            $shadowAfterEl = swiper.$(
              `<div class="swiper-slide-shadow-${
                isHorizontal ? 'right' : 'bottom'
              }"></div>`,
            );
            $slideEl.append($shadowAfterEl);
          }
          if ($shadowBeforeEl.length)
            $shadowBeforeEl[0].style.opacity =
              offsetMultiplier > 0 ? offsetMultiplier : 0;
          if ($shadowAfterEl.length)
            $shadowAfterEl[0].style.opacity =
              -offsetMultiplier > 0 ? -offsetMultiplier : 0;
        }
      }

      // Set correct perspective for IE10
      if (
        swiper.support.pointerEvents ||
        swiper.support.prefixedPointerEvents
      ) {
        const ws = $wrapperEl[0].style;
        ws.perspectiveOrigin = `${center}px 50%`;
      }
    },
    setTransition(duration) {
      const swiper = this;
      swiper.slides
        .transition(duration)
        .find(
          '.swiper-slide-shadow-top, .swiper-slide-shadow-right, .swiper-slide-shadow-bottom, .swiper-slide-shadow-left',
        )
        .transition(duration);
    },
  },
};
```

### Cube

```tsx
const slideOpts = {
  grabCursor: true,
  cubeEffect: {
    shadow: true,
    slideShadows: true,
    shadowOffset: 20,
    shadowScale: 0.94,
  },
  on: {
    beforeInit: function () {
      const swiper = this;
      swiper.classNames.push(`${swiper.params.containerModifierClass}cube`);
      swiper.classNames.push(`${swiper.params.containerModifierClass}3d`);

      const overwriteParams = {
        slidesPerView: 1,
        slidesPerColumn: 1,
        slidesPerGroup: 1,
        watchSlidesProgress: true,
        resistanceRatio: 0,
        spaceBetween: 0,
        centeredSlides: false,
        virtualTranslate: true,
      };

      this.params = Object.assign(this.params, overwriteParams);
      this.originalParams = Object.assign(this.originalParams, overwriteParams);
    },
    setTranslate: function () {
      const swiper = this;
      const {
        $el,
        $wrapperEl,
        slides,
        width: swiperWidth,
        height: swiperHeight,
        rtlTranslate: rtl,
        size: swiperSize,
      } = swiper;
      const params = swiper.params.cubeEffect;
      const isHorizontal = swiper.isHorizontal();
      const isVirtual = swiper.virtual && swiper.params.virtual.enabled;
      let wrapperRotate = 0;
      let $cubeShadowEl;
      if (params.shadow) {
        if (isHorizontal) {
          $cubeShadowEl = $wrapperEl.find('.swiper-cube-shadow');
          if ($cubeShadowEl.length === 0) {
            $cubeShadowEl = swiper.$('<div class="swiper-cube-shadow"></div>');
            $wrapperEl.append($cubeShadowEl);
          }
          $cubeShadowEl.css({ height: `${swiperWidth}px` });
        } else {
          $cubeShadowEl = $el.find('.swiper-cube-shadow');
          if ($cubeShadowEl.length === 0) {
            $cubeShadowEl = swiper.$('<div class="swiper-cube-shadow"></div>');
            $el.append($cubeShadowEl);
          }
        }
      }

      for (let i = 0; i < slides.length; i += 1) {
        const $slideEl = slides.eq(i);
        let slideIndex = i;
        if (isVirtual) {
          slideIndex = parseInt($slideEl.attr('data-swiper-slide-index'), 10);
        }
        let slideAngle = slideIndex * 90;
        let round = Math.floor(slideAngle / 360);
        if (rtl) {
          slideAngle = -slideAngle;
          round = Math.floor(-slideAngle / 360);
        }
        const progress = Math.max(Math.min($slideEl[0].progress, 1), -1);
        let tx = 0;
        let ty = 0;
        let tz = 0;
        if (slideIndex % 4 === 0) {
          tx = -round * 4 * swiperSize;
          tz = 0;
        } else if ((slideIndex - 1) % 4 === 0) {
          tx = 0;
          tz = -round * 4 * swiperSize;
        } else if ((slideIndex - 2) % 4 === 0) {
          tx = swiperSize + round * 4 * swiperSize;
          tz = swiperSize;
        } else if ((slideIndex - 3) % 4 === 0) {
          tx = -swiperSize;
          tz = 3 * swiperSize + swiperSize * 4 * round;
        }
        if (rtl) {
          tx = -tx;
        }

        if (!isHorizontal) {
          ty = tx;
          tx = 0;
        }

        const transform$$1 = `rotateX(${
          isHorizontal ? 0 : -slideAngle
        }deg) rotateY(${
          isHorizontal ? slideAngle : 0
        }deg) translate3d(${tx}px, ${ty}px, ${tz}px)`;
        if (progress <= 1 && progress > -1) {
          wrapperRotate = slideIndex * 90 + progress * 90;
          if (rtl) wrapperRotate = -slideIndex * 90 - progress * 90;
        }
        $slideEl.transform(transform$$1);
        if (params.slideShadows) {
          // Set shadows
          let shadowBefore = isHorizontal
            ? $slideEl.find('.swiper-slide-shadow-left')
            : $slideEl.find('.swiper-slide-shadow-top');
          let shadowAfter = isHorizontal
            ? $slideEl.find('.swiper-slide-shadow-right')
            : $slideEl.find('.swiper-slide-shadow-bottom');
          if (shadowBefore.length === 0) {
            shadowBefore = swiper.$(
              `<div class="swiper-slide-shadow-${
                isHorizontal ? 'left' : 'top'
              }"></div>`,
            );
            $slideEl.append(shadowBefore);
          }
          if (shadowAfter.length === 0) {
            shadowAfter = swiper.$(
              `<div class="swiper-slide-shadow-${
                isHorizontal ? 'right' : 'bottom'
              }"></div>`,
            );
            $slideEl.append(shadowAfter);
          }
          if (shadowBefore.length)
            shadowBefore[0].style.opacity = Math.max(-progress, 0);
          if (shadowAfter.length)
            shadowAfter[0].style.opacity = Math.max(progress, 0);
        }
      }
      $wrapperEl.css({
        '-webkit-transform-origin': `50% 50% -${swiperSize / 2}px`,
        '-moz-transform-origin': `50% 50% -${swiperSize / 2}px`,
        '-ms-transform-origin': `50% 50% -${swiperSize / 2}px`,
        'transform-origin': `50% 50% -${swiperSize / 2}px`,
      });

      if (params.shadow) {
        if (isHorizontal) {
          $cubeShadowEl.transform(
            `translate3d(0px, ${swiperWidth / 2 + params.shadowOffset}px, ${
              -swiperWidth / 2
            }px) rotateX(90deg) rotateZ(0deg) scale(${params.shadowScale})`,
          );
        } else {
          const shadowAngle =
            Math.abs(wrapperRotate) -
            Math.floor(Math.abs(wrapperRotate) / 90) * 90;
          const multiplier =
            1.5 -
            (Math.sin((shadowAngle * 2 * Math.PI) / 360) / 2 +
              Math.cos((shadowAngle * 2 * Math.PI) / 360) / 2);
          const scale1 = params.shadowScale;
          const scale2 = params.shadowScale / multiplier;
          const offset$$1 = params.shadowOffset;
          $cubeShadowEl.transform(
            `scale3d(${scale1}, 1, ${scale2}) translate3d(0px, ${
              swiperHeight / 2 + offset$$1
            }px, ${-swiperHeight / 2 / scale2}px) rotateX(-90deg)`,
          );
        }
      }

      const zFactor =
        swiper.browser.isSafari || swiper.browser.isUiWebView
          ? -swiperSize / 2
          : 0;
      $wrapperEl.transform(
        `translate3d(0px,0,${zFactor}px) rotateX(${
          swiper.isHorizontal() ? 0 : wrapperRotate
        }deg) rotateY(${swiper.isHorizontal() ? -wrapperRotate : 0}deg)`,
      );
    },
    setTransition: function (duration) {
      const swiper = this;
      const { $el, slides } = swiper;
      slides
        .transition(duration)
        .find(
          '.swiper-slide-shadow-top, .swiper-slide-shadow-right, .swiper-slide-shadow-bottom, .swiper-slide-shadow-left',
        )
        .transition(duration);
      if (swiper.params.cubeEffect.shadow && !swiper.isHorizontal()) {
        $el.find('.swiper-cube-shadow').transition(duration);
      }
    },
  },
};
```

### Fade

```tsx
const slideOpts = {
  on: {
    beforeInit() {
      const swiper = this;
      swiper.classNames.push(`${swiper.params.containerModifierClass}fade`);
      const overwriteParams = {
        slidesPerView: 1,
        slidesPerColumn: 1,
        slidesPerGroup: 1,
        watchSlidesProgress: true,
        spaceBetween: 0,
        virtualTranslate: true,
      };
      swiper.params = Object.assign(swiper.params, overwriteParams);
      swiper.params = Object.assign(swiper.originalParams, overwriteParams);
    },
    setTranslate() {
      const swiper = this;
      const { slides } = swiper;
      for (let i = 0; i < slides.length; i += 1) {
        const $slideEl = swiper.slides.eq(i);
        const offset$$1 = $slideEl[0].swiperSlideOffset;
        let tx = -offset$$1;
        if (!swiper.params.virtualTranslate) tx -= swiper.translate;
        let ty = 0;
        if (!swiper.isHorizontal()) {
          ty = tx;
          tx = 0;
        }
        const slideOpacity = swiper.params.fadeEffect.crossFade
          ? Math.max(1 - Math.abs($slideEl[0].progress), 0)
          : 1 + Math.min(Math.max($slideEl[0].progress, -1), 0);
        $slideEl
          .css({
            opacity: slideOpacity,
          })
          .transform(`translate3d(${tx}px, ${ty}px, 0px)`);
      }
    },
    setTransition(duration) {
      const swiper = this;
      const { slides, $wrapperEl } = swiper;
      slides.transition(duration);
      if (swiper.params.virtualTranslate && duration !== 0) {
        let eventTriggered = false;
        slides.transitionEnd(() => {
          if (eventTriggered) return;
          if (!swiper || swiper.destroyed) return;
          eventTriggered = true;
          swiper.animating = false;
          const triggerEvents = ['webkitTransitionEnd', 'transitionend'];
          for (let i = 0; i < triggerEvents.length; i += 1) {
            $wrapperEl.trigger(triggerEvents[i]);
          }
        });
      }
    },
  },
};
```

### Flip

```tsx
const slideOpts = {
  on: {
    beforeInit() {
      const swiper = this;
      swiper.classNames.push(`${swiper.params.containerModifierClass}flip`);
      swiper.classNames.push(`${swiper.params.containerModifierClass}3d`);
      const overwriteParams = {
        slidesPerView: 1,
        slidesPerColumn: 1,
        slidesPerGroup: 1,
        watchSlidesProgress: true,
        spaceBetween: 0,
        virtualTranslate: true,
      };
      swiper.params = Object.assign(swiper.params, overwriteParams);
      swiper.originalParams = Object.assign(
        swiper.originalParams,
        overwriteParams,
      );
    },
    setTranslate() {
      const swiper = this;
      const { $, slides, rtlTranslate: rtl } = swiper;
      for (let i = 0; i < slides.length; i += 1) {
        const $slideEl = slides.eq(i);
        let progress = $slideEl[0].progress;
        if (swiper.params.flipEffect.limitRotation) {
          progress = Math.max(Math.min($slideEl[0].progress, 1), -1);
        }
        const offset$$1 = $slideEl[0].swiperSlideOffset;
        const rotate = -180 * progress;
        let rotateY = rotate;
        let rotateX = 0;
        let tx = -offset$$1;
        let ty = 0;
        if (!swiper.isHorizontal()) {
          ty = tx;
          tx = 0;
          rotateX = -rotateY;
          rotateY = 0;
        } else if (rtl) {
          rotateY = -rotateY;
        }

        $slideEl[0].style.zIndex =
          -Math.abs(Math.round(progress)) + slides.length;

        if (swiper.params.flipEffect.slideShadows) {
          // Set shadows
          let shadowBefore = swiper.isHorizontal()
            ? $slideEl.find('.swiper-slide-shadow-left')
            : $slideEl.find('.swiper-slide-shadow-top');
          let shadowAfter = swiper.isHorizontal()
            ? $slideEl.find('.swiper-slide-shadow-right')
            : $slideEl.find('.swiper-slide-shadow-bottom');
          if (shadowBefore.length === 0) {
            shadowBefore = swiper.$(
              `<div class="swiper-slide-shadow-${
                swiper.isHorizontal() ? 'left' : 'top'
              }"></div>`,
            );
            $slideEl.append(shadowBefore);
          }
          if (shadowAfter.length === 0) {
            shadowAfter = swiper.$(
              `<div class="swiper-slide-shadow-${
                swiper.isHorizontal() ? 'right' : 'bottom'
              }"></div>`,
            );
            $slideEl.append(shadowAfter);
          }
          if (shadowBefore.length)
            shadowBefore[0].style.opacity = Math.max(-progress, 0);
          if (shadowAfter.length)
            shadowAfter[0].style.opacity = Math.max(progress, 0);
        }
        $slideEl.transform(
          `translate3d(${tx}px, ${ty}px, 0px) rotateX(${rotateX}deg) rotateY(${rotateY}deg)`,
        );
      }
    },
    setTransition(duration) {
      const swiper = this;
      const { slides, activeIndex, $wrapperEl } = swiper;
      slides
        .transition(duration)
        .find(
          '.swiper-slide-shadow-top, .swiper-slide-shadow-right, .swiper-slide-shadow-bottom, .swiper-slide-shadow-left',
        )
        .transition(duration);
      if (swiper.params.virtualTranslate && duration !== 0) {
        let eventTriggered = false;
        // eslint-disable-next-line
        slides.eq(activeIndex).transitionEnd(function onTransitionEnd() {
          if (eventTriggered) return;
          if (!swiper || swiper.destroyed) return;

          eventTriggered = true;
          swiper.animating = false;
          const triggerEvents = ['webkitTransitionEnd', 'transitionend'];
          for (let i = 0; i < triggerEvents.length; i += 1) {
            $wrapperEl.trigger(triggerEvents[i]);
          }
        });
      }
    },
  },
};
```

## Usage

<Tabs defaultValue="angular" values={[{ value: 'angular', label: 'ANGULAR' }, { value: 'javascript', label: 'JAVASCRIPT' }, { value: 'react', label: 'REACT' }, { value: 'stencil', label: 'STENCIL' }, { value: 'vue', label: 'VUE' }]}>

<TabItem value="angular">

```tsx
import { Component } from '@angular/core';

@Component({
  selector: 'slides-example',
  template: `
    <ion-content>
      <ion-slides pager="true" [options]="slideOpts">
        <ion-slide>
          <h1>Slide 1</h1>
        </ion-slide>
        <ion-slide>
          <h1>Slide 2</h1>
        </ion-slide>
        <ion-slide>
          <h1>Slide 3</h1>
        </ion-slide>
      </ion-slides>
    </ion-content>
  `,
})
export class SlideExample {
  // Optional parameters to pass to the swiper instance.
  // See http://idangero.us/swiper/api/ for valid options.
  slideOpts = {
    initialSlide: 1,
    speed: 400,
  };
  constructor() {}
}
```

```css
/* Without setting height the slides will take up the height of the slide's content */
ion-slides {
  height: 100%;
}
```

</TabItem>

<TabItem value="javascript">

```html
<ion-content>
  <ion-slides pager="true">
    <ion-slide>
      <h1>Slide 1</h1>
    </ion-slide>

    <ion-slide>
      <h1>Slide 2</h1>
    </ion-slide>

    <ion-slide>
      <h1>Slide 3</h1>
    </ion-slide>
  </ion-slides>
</ion-content>
```

```javascript
var slides = document.querySelector('ion-slides');

// Optional parameters to pass to the swiper instance.
// See http://idangero.us/swiper/api/ for valid options.
slides.options = {
  initialSlide: 1,
  speed: 400,
};
```

```css
/* Without setting height the slides will take up the height of the slide's content */
ion-slides {
  height: 100%;
}
```

</TabItem>

<TabItem value="react">

```tsx
import React from 'react';
import { IonSlides, IonSlide, IonContent } from '@ionic/react';

// Optional parameters to pass to the swiper instance.
// See http://idangero.us/swiper/api/ for valid options.
const slideOpts = {
  initialSlide: 1,
  speed: 400,
};

export const SlidesExample: React.FC = () => (
  <IonContent>
    <IonSlides pager={true} options={slideOpts}>
      <IonSlide>
        <h1>Slide 1</h1>
      </IonSlide>
      <IonSlide>
        <h1>Slide 2</h1>
      </IonSlide>
      <IonSlide>
        <h1>Slide 3</h1>
      </IonSlide>
    </IonSlides>
  </IonContent>
);
```

```css
/* Without setting height the slides will take up the height of the slide's content */
ion-slides {
  height: 100%;
}
```

</TabItem>

<TabItem value="stencil">

```tsx
import { Component, h } from '@stencil/core';

@Component({
  tag: 'slides-example',
  styleUrl: 'slides-example.css',
})
export class SlidesExample {
  // Optional parameters to pass to the swiper instance.
  // See http://idangero.us/swiper/api/ for valid options.
  private slideOpts = {
    initialSlide: 1,
    speed: 400,
  };

  render() {
    return [
      <ion-content>
        <ion-slides pager={true} options={this.slideOpts}>
          <ion-slide>
            <h1>Slide 1</h1>
          </ion-slide>

          <ion-slide>
            <h1>Slide 2</h1>
          </ion-slide>

          <ion-slide>
            <h1>Slide 3</h1>
          </ion-slide>
        </ion-slides>
      </ion-content>,
    ];
  }
}
```

```css
/* Without setting height the slides will take up the height of the slide's content */
ion-slides {
  height: 100%;
}
```

</TabItem>

<TabItem value="vue">

```html
<template>
  <ion-slides pager="true" :options="slideOpts">
    <ion-slide>
      <h1>Slide 1</h1>
    </ion-slide>
    <ion-slide>
      <h1>Slide 2</h1>
    </ion-slide>
    <ion-slide>
      <h1>Slide 3</h1>
    </ion-slide>
  </ion-slides>
</template>

<script>
  import { IonSlides, IonSlide } from '@ionic/vue';
  import { defineComponent } from 'vue';

  export default defineComponent({
    components: { IonSlides, IonSlide },
    setup() {
      // Optional parameters to pass to the swiper instance. See http://idangero.us/swiper/api/ for valid options.
      const slideOpts = {
        initialSlide: 1,
        speed: 400,
      };
      return { slideOpts };
    },
  });
</script>
```

</TabItem>

</Tabs>

## Properties

### mode

|                 |                                                   |
| --------------- | ------------------------------------------------- |
| **Description** | The mode determines which platform styles to use. |
| **Attribute**   | `mode`                                            |
| **Type**        | `"ios" \| "md"`                                   |
| **Default**     | `undefined`                                       |

### options

|                 |                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------- |
| **Description** | Options to pass to the swiper instance.<br />See http://idangero.us/swiper/api/ for valid options |
| **Attribute**   | `options`                                                                                         |
| **Type**        | `any`                                                                                             |
| **Default**     | `{}`                                                                                              |

### pager

|                 |                                 |
| --------------- | ------------------------------- |
| **Description** | If `true`, show the pagination. |
| **Attribute**   | `pager`                         |
| **Type**        | `boolean`                       |
| **Default**     | `false`                         |

### scrollbar

|                 |                                |
| --------------- | ------------------------------ |
| **Description** | If `true`, show the scrollbar. |
| **Attribute**   | `scrollbar`                    |
| **Type**        | `boolean`                      |
| **Default**     | `false`                        |

## Events

| Name                      | Description                                                 |
| ------------------------- | ----------------------------------------------------------- |
| `ionSlideDidChange`       | Emitted after the active slide has changed.                 |
| `ionSlideDoubleTap`       | Emitted when the user double taps on the slide's container. |
| `ionSlideDrag`            | Emitted when the slider is actively being moved.            |
| `ionSlideNextEnd`         | Emitted when the next slide has ended.                      |
| `ionSlideNextStart`       | Emitted when the next slide has started.                    |
| `ionSlidePrevEnd`         | Emitted when the previous slide has ended.                  |
| `ionSlidePrevStart`       | Emitted when the previous slide has started.                |
| `ionSlideReachEnd`        | Emitted when the slider is at the last slide.               |
| `ionSlideReachStart`      | Emitted when the slider is at its initial position.         |
| `ionSlidesDidLoad`        | Emitted after Swiper initialization                         |
| `ionSlideTap`             | Emitted when the user taps/clicks on the slide's container. |
| `ionSlideTouchEnd`        | Emitted when the user releases the touch.                   |
| `ionSlideTouchStart`      | Emitted when the user first touches the slider.             |
| `ionSlideTransitionEnd`   | Emitted when the slide transition has ended.                |
| `ionSlideTransitionStart` | Emitted when the slide transition has started.              |
| `ionSlideWillChange`      | Emitted before the active slide has changed.                |

## Methods

### getActiveIndex

|                 |                                       |
| --------------- | ------------------------------------- |
| **Description** | Get the index of the active slide.    |
| **Signature**   | `getActiveIndex() => Promise<number>` |

### getPreviousIndex

|                 |                                         |
| --------------- | --------------------------------------- |
| **Description** | Get the index of the previous slide.    |
| **Signature**   | `getPreviousIndex() => Promise<number>` |

### getSwiper

|                 |                                                                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **Description** | Get the Swiper instance.<br />Use this to access the full Swiper API.<br />See https://idangero.us/swiper/api/ for all API options. |
| **Signature**   | `getSwiper() => Promise<any>`                                                                                                       |

### isBeginning

|                 |                                                          |
| --------------- | -------------------------------------------------------- |
| **Description** | Get whether or not the current slide is the first slide. |
| **Signature**   | `isBeginning() => Promise<boolean>`                      |

### isEnd

|                 |                                                         |
| --------------- | ------------------------------------------------------- |
| **Description** | Get whether or not the current slide is the last slide. |
| **Signature**   | `isEnd() => Promise<boolean>`                           |

### length

|                 |                                 |
| --------------- | ------------------------------- |
| **Description** | Get the total number of slides. |
| **Signature**   | `length() => Promise<number>`   |

### lockSwipeToNext

|                 |                                                        |
| --------------- | ------------------------------------------------------ |
| **Description** | Lock or unlock the ability to slide to the next slide. |
| **Signature**   | `lockSwipeToNext(lock: boolean) => Promise<void>`      |

### lockSwipeToPrev

|                 |                                                            |
| --------------- | ---------------------------------------------------------- |
| **Description** | Lock or unlock the ability to slide to the previous slide. |
| **Signature**   | `lockSwipeToPrev(lock: boolean) => Promise<void>`          |

### lockSwipes

|                 |                                                                    |
| --------------- | ------------------------------------------------------------------ |
| **Description** | Lock or unlock the ability to slide to the next or previous slide. |
| **Signature**   | `lockSwipes(lock: boolean) => Promise<void>`                       |

### slideNext

|                 |                                                                                                |
| --------------- | ---------------------------------------------------------------------------------------------- |
| **Description** | Transition to the next slide.                                                                  |
| **Signature**   | `slideNext(speed?: number \| undefined, runCallbacks?: boolean \| undefined) => Promise<void>` |

### slidePrev

|                 |                                                                                                |
| --------------- | ---------------------------------------------------------------------------------------------- |
| **Description** | Transition to the previous slide.                                                              |
| **Signature**   | `slidePrev(speed?: number \| undefined, runCallbacks?: boolean \| undefined) => Promise<void>` |

### slideTo

|                 |                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------- |
| **Description** | Transition to the specified slide.                                                                          |
| **Signature**   | `slideTo(index: number, speed?: number \| undefined, runCallbacks?: boolean \| undefined) => Promise<void>` |

### startAutoplay

|                 |                                    |
| --------------- | ---------------------------------- |
| **Description** | Start auto play.                   |
| **Signature**   | `startAutoplay() => Promise<void>` |

### stopAutoplay

|                 |                                   |
| --------------- | --------------------------------- |
| **Description** | Stop auto play.                   |
| **Signature**   | `stopAutoplay() => Promise<void>` |

### update

|                 |                                                                                                      |
| --------------- | ---------------------------------------------------------------------------------------------------- |
| **Description** | Update the underlying slider implementation. Call this if you've added or removed<br />child slides. |
| **Signature**   | `update() => Promise<void>`                                                                          |

### updateAutoHeight

|                 |                                                                                                                  |
| --------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Description** | Force swiper to update its height (when autoHeight is enabled) for the duration<br />equal to 'speed' parameter. |
| **Signature**   | `updateAutoHeight(speed?: number \| undefined) => Promise<void>`                                                 |

## CSS Custom Properties

| Name                               | Description                                      |
| ---------------------------------- | ------------------------------------------------ |
| `--bullet-background`              | Background of the pagination bullets             |
| `--bullet-background-active`       | Background of the active pagination bullet       |
| `--progress-bar-background`        | Background of the pagination progress-bar        |
| `--progress-bar-background-active` | Background of the active pagination progress-bar |
| `--scroll-bar-background`          | Background of the pagination scroll-bar          |
| `--scroll-bar-background-active`   | Background of the active pagination scroll-bar   |
