为了实现遮罩效果动画。android本身没有提供api ，需要自己动手实现。
将view和view的parentLy进行相反方向动画即可实现该效果：

```
AnimatorSet animatorSet = new AnimatorSet();
//背景layout
ObjectAnimator bgLy = ObjectAnimator.ofFloat(loginShowContainerLy,
                "translationX", -measureWidth(loginShowContainerLy), 0.0f);
        bgLy.setDuration(showLoginBgEnterTime);
        bgLy.setInterpolator(new LinearInterpolator());
ObjectAnimator view = ObjectAnimator.ofFloat(loginShowView,
                "translationX", measureWidth(loginShowView), 0.0f);
        viewLy.setDuration(showLoginBgEnterTime);
        viewLy.setInterpolator(new LinearInterpolator());

animatorSet.play(bgLy).with(view);
animatorSet.start();
```