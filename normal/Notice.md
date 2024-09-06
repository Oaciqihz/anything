
# Taro (小程序)

## Animate

### 未知高度實現展開關閉動畫
- 封裝
```ts
import gsap from 'gsap';

class GsapWrapper {
  // 關閉動畫
  static AlertExpandOff = (
    el: HTMLElement | string,
    onComplete: () => void,
  ) => {
    gsap.to(el, {
      duration: 0.1, // 动画持续时间
      height: 0, // 最终高度为 0
      opacity: 0, // 最终透明度为 0
    //   ease: 'power1.out', // 使用缓动函数使动画更平滑
      onComplete: () => {
        gsap.delayedCall(0, onComplete);
      },
    });
  };
  // 打開動畫
  static AlertExpandOn = (
    el: HTMLElement | string,
    onComplete?: () => void,
  ) => {
    gsap.delayedCall(0, () => {
      gsap.fromTo(
        el,
        {
          duration: 0.2, // 动画持续时间
          height: 0, // 最终高度为 0
          opacity: 0, // 最终透明度为 0
        //   ease: 'power1.out', // 使用缓动函数使动画更平滑
        },
        {
          duration: 0.2, // 动画持续时间
          height: 'auto', // 最终高度为 auto
          opacity: 1, // 最终透明度为 1
        //   ease: 'power1.out', // 使用缓动函数使动画更平滑
          onComplete: onComplete,
        },
      );
    });
  };
}
export default GsapWrapper;
```
- demo
```tsx
  const wrapperRef = useRef<HTMLDivElement | null>(null);
  GsapWrapper.AlertExpandOff(wrapperRef.current, () => {});
```
