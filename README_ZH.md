# Tomasulo's Algorithm
## 注意事項
* 使用的 **instruction** 有 **ADD** , **ADDI** , **SUB** , **MUL** , **DIV** !!務必使用大寫!! 
* 目前RF上限支援5個
* 本程式初始限制條件RF1~5依序初始值為0,2,4,6,8 
* Cycle等待時間 
  - ADD/SUB : 2 cycles
  - MUL     : 4 cycles
  - DIV     : 8 cycles
* **Cycle** 完成後下次 **Cycle** 可以 **Write Result** 
* **Write Result** 的值馬上可以使用
* **ALU** 空出後下次 **Cycle** 可以 **Dispatch**


## 資料說明
* **test.txt** 的輸出為 **cout.txt**  

## 輸出範例
輸出資料僅輸出有完成Dispatch或者是內容有變化之Cycle                    
RS1.2.3為ADD/SUB的暫存，共3個                     
RS4.5為MUL/DIV的暫存，共2個                     

```
----------IN File----------
ADDI F1, F2, 1
SUB F1, F3, F4
DIV F1, F3, F2
MUL F2, F3, F4
ADD F2, F4, F2
ADDI F4, F1, 2
MUL F5, F5, F5
ADD F1, F4, F4
---------------------------
Cycle: 1
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS1 |
F2 |      |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |    2 |    1 |
RS2 |      |      |      |
RS3 |      |      |      |
    ----------------------
BUFFER: empty

    ______________________
RS4 |      |      |      |
RS5 |      |      |      |
    ----------------------
BUFFER: empty

Cycle: 2
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS2 |
F2 |      |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |    2 |    1 |
RS2 |    - |    4 |    6 |
RS3 |      |      |      |
    ----------------------
BUFFER: (RS1 Dispatch)2 + 1 

    ______________________
RS4 |      |      |      |
RS5 |      |      |      |
    ----------------------
BUFFER: empty

Cycle: 3
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |      |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |    2 |    1 |
RS2 |    - |    4 |    6 |
RS3 |      |      |      |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    / |    4 |    2 |
RS5 |      |      |      |
    ----------------------
BUFFER: empty

Cycle: 4
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS5 |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |    2 |    1 |
RS2 |    - |    4 |    6 |
RS3 |      |      |      |
    ----------------------
BUFFER: (RS1 Dispatch Done)2 + 1 Next Cycle Can Write Result

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: (RS4 Dispatch)4 / 2 

Cycle: 5
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |    - |    4 |    6 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: (RS2 Dispatch)4 - 6 

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 6
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |  RS4 |    2 |
RS2 |    - |    4 |    6 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 7
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |  RS4 |    2 |
RS2 |    - |    4 |    6 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: (RS2 Dispatch Done)4 - 6 Next Cycle Can Write Result

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 8
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |  RS4 |    2 |
RS2 |      |      |      |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 12
   _ RF __
F1 |   0 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS4 |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |  RS4 |    2 |
RS2 |      |      |      |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    / |    4 |    2 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: (RS4 Dispatch Done)4 / 2 Next Cycle Can Write Result

Cycle: 13
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |      |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |      |
   --------

    _ RS _________________
RS1 |    + |    2 |    2 |
RS2 |      |      |      |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: (RS1 Dispatch)2 + 2 

    ______________________
RS4 |      |      |      |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: (RS5 Dispatch)4 * 6 

Cycle: 14
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |      |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |    + |    2 |    2 |
RS2 |      |      |      |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    * |    8 |    8 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 15
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   6 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS2 |
F2 |  RS3 |
F3 |      |
F4 |  RS1 |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |    + |    2 |    2 |
RS2 |    + |  RS1 |  RS1 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: (RS1 Dispatch Done)2 + 2 Next Cycle Can Write Result

    ______________________
RS4 |    * |    8 |    8 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 16
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS2 |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |    + |    4 |    4 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: (RS2 Dispatch)4 + 4 

    ______________________
RS4 |    * |    8 |    8 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: empty

Cycle: 17
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS2 |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |    + |    4 |    4 |
RS3 |    + |    6 |  RS5 |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    * |    8 |    8 |
RS5 |    * |    4 |    6 |
    ----------------------
BUFFER: (RS5 Dispatch Done)4 * 6 Next Cycle Can Write Result

Cycle: 18
   _ RF __
F1 |   2 |
F2 |   2 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |  RS2 |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |    + |    4 |    4 |
RS3 |    + |    6 |   24 |
    ----------------------
BUFFER: (RS2 Dispatch Done)4 + 4 Next Cycle Can Write Result

    ______________________
RS4 |    * |    8 |    8 |
RS5 |      |      |      |
    ----------------------
BUFFER: (RS4 Dispatch)8 * 8 

Cycle: 19
   _ RF __
F1 |   8 |
F2 |   2 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |      |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |      |      |      |
RS3 |    + |    6 |   24 |
    ----------------------
BUFFER: (RS3 Dispatch)6 + 24 

    ______________________
RS4 |    * |    8 |    8 |
RS5 |      |      |      |
    ----------------------
BUFFER: empty

Cycle: 21
   _ RF __
F1 |   8 |
F2 |   2 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |      |
F2 |  RS3 |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |      |      |      |
RS3 |    + |    6 |   24 |
    ----------------------
BUFFER: (RS3 Dispatch Done)6 + 24 Next Cycle Can Write Result

    ______________________
RS4 |    * |    8 |    8 |
RS5 |      |      |      |
    ----------------------
BUFFER: empty

Cycle: 22
   _ RF __
F1 |   8 |
F2 |  30 |
F3 |   4 |
F4 |   4 |
F5 |   8 |
   -------

   _ RAT __
F1 |      |
F2 |      |
F3 |      |
F4 |      |
F5 |  RS4 |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |      |      |      |
RS3 |      |      |      |
    ----------------------
BUFFER: empty

    ______________________
RS4 |    * |    8 |    8 |
RS5 |      |      |      |
    ----------------------
BUFFER: (RS4 Dispatch Done)8 * 8 Next Cycle Can Write Result

Cycle: 23
   _ RF __
F1 |   8 |
F2 |  30 |
F3 |   4 |
F4 |   4 |
F5 |  64 |
   -------

   _ RAT __
F1 |      |
F2 |      |
F3 |      |
F4 |      |
F5 |      |
   --------

    _ RS _________________
RS1 |      |      |      |
RS2 |      |      |      |
RS3 |      |      |      |
    ----------------------
BUFFER: empty

    ______________________
RS4 |      |      |      |
RS5 |      |      |      |
    ----------------------
BUFFER: empty


```
