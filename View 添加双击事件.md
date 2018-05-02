```
class onDoubleClick implements View.OnTouchListener{
        int count = 0;
        long firClick;
        long secClick;
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            if(MotionEvent.ACTION_DOWN == event.getAction()){
                count++;
                if(count == 1){
                    firClick = System.currentTimeMillis();
                } else if (count == 2){
                    secClick = System.currentTimeMillis();
                    if(secClick - firClick < 1000){
                        //双击事件
                      
                    }
                    count = 0;
                    firClick = 0;
                    secClick = 0;

                }
            }
            return true;
        }

    }
```
转发：http://jasonshieh.iteye.com/blog/751068