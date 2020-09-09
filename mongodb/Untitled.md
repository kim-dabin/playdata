```java
import java.util.*;
class Solution {
    public int[] solution(int[] prices) {
         int length = prices.length;
        int[] answer = new int[length];
       
        for(int i=0; i< length; i++ ){
            if(i+1>=length) break;
            answer[i]=prices[i]<=prices[i+1]?0:-1;
        }
        
        int idx = 0;
        for(int a : answer){
            answer[idx] = a+(length-1);
            length--;
            idx++;
        }
        
        return answer;
    }
}
```

