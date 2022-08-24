# 코드

<details>
  <summary>작성한 JAVA 코드</summary>
  
``` java
import java.io.*;

public class Main {
  
    public static void main(String[] args) throws IOException {
  
        int[] testArr = {7,1,5,3,4,2,6};

        quickSort(testArr, 0, testArr.length-1);
    }
  
    public static void quickSort(int[] arr, int left, int right) {
        int pivot = arr[(left+right)/2];
        int markerLeft = left, markerRight= right;
        int temp;

        while(markerLeft < markerRight) {
            // each iteration
            // move markers(markerLeft , markerRight)
            // until each condition is met
            while(arr[markerLeft] < pivot) {
                markerLeft++;
            }
            while(arr[markerRight] > pivot) {
                markerRight--;
            }

            printBeforeState(arr,left,right,pivot,markerLeft,markerRight);

            // Swap
            if(markerLeft <= markerRight) {
                temp = arr[markerLeft];
                arr[markerLeft] = arr[markerRight];
                arr[markerRight] = temp;
                markerLeft++;
                markerRight--;
            }

            printAfterState(arr,left,right,pivot,markerLeft,markerRight);
        }

        // recursion check
        if(left < markerRight) {
            printLeftRecursionState(left,markerRight);

            quickSort(arr, left, markerRight); // call Recursionally
        }
        if(markerLeft < right) {
            printRightRecursionState(markerLeft,right);

            quickSort(arr, markerLeft, right); // call Recursionally
        }
    }


    // --------------------------------
    // BELOW functions are for printing values of variables in each iteration.
    private static void printBeforeState(int[] arr, int left, int right, int pivot, int markerLeft, int markerRight) {
        System.out.println("--------------------------");
        System.out.println("quickSort(arr, "+left+", "+right+")");
        System.out.println("left (idx) : " + left);
        System.out.println("right (idx) : " + right);
        System.out.println("pivot (value) : " + pivot);
        System.out.println("markerLeft (idx) : " + markerLeft);
        System.out.println("markerRight (idx) : " + markerRight);

        System.out.println("##### Before sort #####");
        for(int e : arr) {
            System.out.print(e + " ");
        }
        System.out.println();
    }

    private static void printAfterState(int[] arr, int left, int right, int pivot, int markerLeft, int markerRight) {
        System.out.println("~~~~~ After markerVars ~~~~~");

        System.out.println("markerLeft (idx) : " + markerLeft);
        System.out.println("markerRight (idx) : " + markerRight);
        System.out.println("~~~~~~~~~~~~~~~~~~~~~~~~~~~~");

        System.out.println("##### After sort #####");
        for(int e : arr) {
            System.out.print(e + " ");
        }
        System.out.println();
        System.out.println("--------------------------");

        System.out.println();
        System.out.println();
    }

    private static void printLeftRecursionState(int left, int markerRight) {
        System.out.println("------------------------");
        System.out.println("Recursion Call!!!");
        System.out.println("CALL Left partition -> quickSort(arr, markerLeft, right)");
        System.out.println("quickSort(arr, " + left + ", " + markerRight + "); ");
        System.out.println("------------------------");
    }

    private static void printRightRecursionState(int markerLeft, int right) {
        System.out.println("------------------------");
        System.out.println("Recursion Call!!!");
        System.out.println("CALL right partition -> quickSort(arr, markerLeft, right)");
        System.out.println("quickSort(arr, " + markerLeft + ", " + right + "); ");
        System.out.println("------------------------");
    }
}
```
</details>
  
---

# QuickSort 그림으로 설명

![슬라이드1](https://user-images.githubusercontent.com/101965836/186377648-12594549-b38d-4239-9263-4e81c066eef0.PNG)  
  
  ---
  
![슬라이드2](https://user-images.githubusercontent.com/101965836/186377653-5a0f0bce-bafd-4b3e-b66c-8760aea07c8e.PNG)  
  
  ---
  
![슬라이드3](https://user-images.githubusercontent.com/101965836/186377654-4e4a8654-464c-4214-91e4-3a7fc059d8ec.PNG)  
  
  ---
  
![슬라이드4](https://user-images.githubusercontent.com/101965836/186377659-812e0577-ee10-4fc4-8fd3-81613e6f3e8a.PNG)  
  
  ---
  
![슬라이드5](https://user-images.githubusercontent.com/101965836/186377662-fc8dafd7-1427-4eb1-9d99-a2bfc9e82976.PNG)  
  
  ---
  
![슬라이드6](https://user-images.githubusercontent.com/101965836/186377665-67786c8f-9a54-4573-8c1f-fbc6c8e00109.PNG)  
  
  ---
  
![슬라이드7](https://user-images.githubusercontent.com/101965836/186377669-8eb2c3b5-ff90-453e-a255-5cad3b8d8649.PNG)  
  
  ---
  
![슬라이드8](https://user-images.githubusercontent.com/101965836/186377671-48c5223e-de18-4de9-9751-6c6cdbc331ac.PNG)  
  
  ---
  
![슬라이드9](https://user-images.githubusercontent.com/101965836/186377672-9e0bfe48-e7c0-4216-8b66-d0ac56bf6888.PNG)  
  
  ---
  
![슬라이드10](https://user-images.githubusercontent.com/101965836/186377677-59dcae5c-7f5b-492e-a6bf-35fb5a27835f.PNG)  
  
  ---
  
![슬라이드11](https://user-images.githubusercontent.com/101965836/186377680-d559bf99-24b7-44a3-a10b-72907d27d22a.PNG)  
  
  ---
  
![슬라이드12](https://user-images.githubusercontent.com/101965836/186377683-6ecf551a-7603-4c95-8b68-fb2795f1883c.PNG)  
  
  ---
  
![슬라이드13](https://user-images.githubusercontent.com/101965836/186377687-45fb9332-c0b3-44e8-a931-b0e8eb64f46f.PNG)  
  
  ---
  
![슬라이드14](https://user-images.githubusercontent.com/101965836/186377690-99976449-b070-4243-837e-9fde3aa1aadc.PNG)  
  
  ---
  
