# 41243233
#### 組員:  郭思翰, 潘彥愷(41243251)
  
## 作業二 第一題

## 解題說明
本題要求設計一個符合抽象資料型態（ADT）5.2 所描述的 最小優先佇列（Min Priority Queue）的 C++ 抽象類別 ```MinPQ```，並進一步撰寫一個從中繼承的具體類別 ```MinHeap```，其內部使用 **最小堆 (min-heap)** 實作各項操作， 包括：檢查是否為空、取出最小元素、插入新元素與移除最小元素。所有操作皆需符合對應 ```MaxHeap``` 函式的時間複雜度要求。  
在設計上，```MinPQ``` 類別以純虛擬函式定義基本操作，提供一個抽象介面，讓實作類別 ```MinHeap``` 依此規格完成具體行為。  

## 解題策略

1. **抽象介面設計**  
先根據題目要求，定義一個最小優先佇列的抽象類別 MinPQ，包含基本操作（是否為空、回傳最小元素、插入元素、刪除最小元素）作為純虛擬函式，確保後續不同實作皆符合統一介面規範。  
2. **具體類別 MinHeap 實作**  
以最小堆（min-heap）為底層資料結構，使用 ```std::vector<T>``` 動態陣列儲存元素，並維護堆的結構性質。  
  &#9679; 實作 ```Push()```：將新元素加入尾端，並透過 ```HeapifyUp 調整堆序性```，維持堆的正確結構。  
  &#9679; 實作 ```Pop()```：將根節點（最小值）與尾端元素交換，移除尾端元素，然後用 ```HeapifyDown``` 恢復堆序性。  
  &#9679; 實作 ```Top()```：回傳堆頂元素即為最小值，需檢查堆是否為空以防止錯誤存取。  
  &#9679; 實作 IsEmpty：利用 ```vector``` 的 ```empty()``` 函式判斷。  
3. **維護操作時間複雜度**  
  &#9679; ```Push``` 與 ```Pop``` 均以 $O(\log n)$ 時間完成，因為堆的高度為 $O(\log n)$，上移與下移調整均在此複雜度。  
  &#9679; ```Top``` 與 ```IsEmpty``` 則為 $O(1)$，直接存取或查詢。  
4. **錯誤處理**  
  &#9679; 在 ```Top``` 與 ```Pop``` 中，若佇列為空時丟出 ```std::runtime_error``` 例外，保證使用安全性與易於偵錯。  
5. **測試驗證**  
編寫主程式利用整數陣列插入元素後，依序取出最小元素，確認堆結構與操作正確性。

## 程式實作
header.h
```cpp
// MinPQ
template <class T>
class MinPQ {
public:
    virtual ~MinPQ() {}                           // 虛擬解構子
    virtual bool IsEmpty() const = 0;             // 回傳佇列是否為空
    virtual const T& Top() const = 0;             // 回傳最小值的參考
    virtual void Push(const T& x) = 0;            // 將元素加入佇列
    virtual void Pop() = 0;                       // 移除最小值元素
};
// MinHeap
#include <vector>
#include <stdexcept>

template <class T>
class MinHeap : public MinPQ<T> {
private:
    std::vector<T> heap;  // 使用 vector 儲存 heap

    void HeapifyUp(int index) {
        while (index > 0) {
            int parent = (index - 1) / 2;
            if (heap[index] < heap[parent]) {
                std::swap(heap[index], heap[parent]);
                index = parent;
            }
            else break;
        }
    }

    void HeapifyDown(int index) {
        int size = heap.size();
        while (index * 2 + 1 < size) {
            int left = index * 2 + 1;
            int right = index * 2 + 2;
            int smallest = index;

            if (left < size && heap[left] < heap[smallest])
                smallest = left;
            if (right < size && heap[right] < heap[smallest])
                smallest = right;

            if (smallest != index) {
                std::swap(heap[index], heap[smallest]);
                index = smallest;
            }
            else break;
        }
    }

public:
    bool IsEmpty() const override {
        return heap.empty();
    }

    const T& Top() const override {
        if (IsEmpty())
            throw std::runtime_error("Heap is empty");
        return heap[0];
    }

    void Push(const T& x) override {
        heap.push_back(x);
        HeapifyUp(heap.size() - 1);
    }

    void Pop() override {
        if (IsEmpty())
            throw std::runtime_error("Heap is empty");
        heap[0] = heap.back();
        heap.pop_back();
        if (!IsEmpty())
            HeapifyDown(0);
    }
};
```
main.cpp
```cpp
//Main
#include <iostream>
#include "MinHeap.h"
int main() {
    MinHeap<int> h;

    int a[11] = { 10, 2, 16, 30, 8, 28, 4, 12, 20, 6, 18 };

    for (int i = 0; i < 11; i++)
        h.Push(a[i]);

    while (!h.IsEmpty()) {
        std::cout << h.Top() << " ";
        h.Pop();
    }

    return 0;
}
```
## 結論  

1. 透過對最小優先佇列的設計與實作，我們加深了對抽象資料型態（ADT）與類別繼承機制的理解，特別是在純虛擬函式設計介面上的應用。
2. 透過實作與測試 ```Push```、```Pop```、```Top``` 等操作，驗證了 min-heap 在維護最小優先佇列時，能有效保持 $O(\log n)$ 或 $O(1)$ 的時間複雜度。
3. 實測過程中，我們也學會如何透過上移與下移維護堆的結構性，進一步體會堆資料結構在實務中的效率與穩定性。
4. 此實作強化了資料結構與演算法在實務應用中的連結，也為後續處理更進階的優先佇列應用打下基礎。
   
## 申論及開發報告  

1. **抽象類別設計**  
設計 ```MinPQ``` 抽象類別，定義統一介面，包含 ```IsEmpty```、```Top```、```Push```、```Pop ```四個操作，便於後續實作不同資料結構版本的最小優先佇列。

2. **使用最小堆實作**  
透過 ```std::vector``` 實作 ```MinHeap``` 類別，維持最小堆結構，保證插入與刪除操作的時間複雜度為 $O(\log n)$，查詢最小值與判斷是否為空的時間為 $O(1)$。

3. **上移與下移操作**  
```HeapifyUp``` 用於插入時維持堆序性，```HeapifyDown``` 用於刪除最小值後重新調整堆結構，兩者為維持正確性的核心。

4. **錯誤處理機制**  
當堆為空時呼叫 ```Top``` 或 ```Pop``` 會丟出例外，確保程式不會異常終止，提升使用者與開發者的安全性。

5. **測試與驗證**  
透過插入與逐一輸出元素的方式測試 ```MinHeap```，驗證其能正確輸出遞增序列，證明堆操作正確無誤。
