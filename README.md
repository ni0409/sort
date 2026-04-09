# 排序演算法分析報告

學號：11428232
姓名：呂星妮 

## 前言
排序演算法是資料結構與程式設計中最基本的主題之一，而不同排序法在資料量增加時，執行效率差異很大，因此本報告將會介紹五種常見排序法，分析其時間與複雜度，並透過程式模擬比較速度等差異。

## 排序法原理介紹
1. 氣泡排序法(Bubble Sort)
- 基本原理：相鄰兩元素比較，若順序錯誤就交換
- 操作方式：每一輪把最大值「浮」到最後
- 特點：簡單，但效率差
2. 選擇排序法(Selection Sort)
- 基本原理：每輪從未排序區間找最小值，放到前面
- 操作方式：每回合只交換一次
- 特點：交換次數少，但比較次數多
3. 插入排序法(Insertion Sort)
- 基本原理：將元素插入前面已排序好的區間
- 操作方式：像整理撲克牌
- 特點：資料近乎有序時表現很好
4. 合併排序法(Merge Sort)
- 基本原理：分治法(分割：把要排的序列平均分成兩半，兩半各別排序、合併：將各別排好的兩半依順序合併在一起)
- 操作方式：遞迴切半，最後把兩個有序陣列合併
- 特點：時間穩定，但需要額外空間
5. 快速排序法(Quick Sort)
- 基本原理：分治法，選 pivot，把資料分成左右兩邊
- 操作方式：小於 pivot 的放左邊，大於 pivot 的放右邊，再遞迴排序
- 特點：平均很快，但最壞情況會退化

## 複雜度分析
| 排序法            |       最佳時間 |       平均時間 |       最壞時間 |    空間複雜度 | 穩定性 |
| -------------- | ---------: | ---------: | ---------: | -------: | --- |
| Bubble Sort    |       O(n) |      O(n²) |      O(n²) |     O(1) | 穩定  |
| Selection Sort |      O(n²) |      O(n²) |      O(n²) |     O(1) | 不穩定 |
| Insertion Sort |       O(n) |      O(n²) |      O(n²) |     O(1) | 穩定  |
| Merge Sort     | O(n log n) | O(n log n) | O(n log n) |     O(n) | 穩定  |
| Quick Sort     | O(n log n) | O(n log n) |      O(n²) | O(log n) | 不穩定 |

## 實驗設計
''' c 
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

void bubble_sort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int swapped = 0;
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = 1;
            }
        }
        if (!swapped) break;
    }
}

void selection_sort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int min_index = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[min_index]) {
                min_index = j;
            }
        }
        int temp = arr[i];
        arr[i] = arr[min_index];
        arr[min_index] = temp;
    }
}

void insertion_sort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    int *L = (int *)malloc(n1 * sizeof(int));
    int *R = (int *)malloc(n2 * sizeof(int));

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int i = 0; i < n2; i++) R[i] = arr[mid + 1 + i];

    int i = 0, j = 0, k = left;

    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) {
            arr[k++] = L[i++];
        } else {
            arr[k++] = R[j++];
        }
    }

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];

    free(L);
    free(R);
}

void merge_sort(int arr[], int left, int right) {
    if (left < right) {
        int mid = (left + right) / 2;
        merge_sort(arr, left, mid);
        merge_sort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }

    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;

    return i + 1;
}

void quick_sort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quick_sort(arr, low, pi - 1);
        quick_sort(arr, pi + 1, high);
    }
}

void copy_array(int source[], int target[], int n) {
    for (int i = 0; i < n; i++) {
        target[i] = source[i];
    }
}

double measure_bubble(int arr[], int n) {
    int *temp = (int *)malloc(n * sizeof(int));
    copy_array(arr, temp, n);

    clock_t start = clock();
    bubble_sort(temp, n);
    clock_t end = clock();

    free(temp);
    return ((double)(end - start)) / CLOCKS_PER_SEC * 1000.0;
}

double measure_selection(int arr[], int n) {
    int *temp = (int *)malloc(n * sizeof(int));
    copy_array(arr, temp, n);

    clock_t start = clock();
    selection_sort(temp, n);
    clock_t end = clock();

    free(temp);
    return ((double)(end - start)) / CLOCKS_PER_SEC * 1000.0;
}

double measure_insertion(int arr[], int n) {
    int *temp = (int *)malloc(n * sizeof(int));
    copy_array(arr, temp, n);

    clock_t start = clock();
    insertion_sort(temp, n);
    clock_t end = clock();

    free(temp);
    return ((double)(end - start)) / CLOCKS_PER_SEC * 1000.0;
}

double measure_merge(int arr[], int n) {
    int *temp = (int *)malloc(n * sizeof(int));
    copy_array(arr, temp, n);

    clock_t start = clock();
    merge_sort(temp, 0, n - 1);
    clock_t end = clock();

    free(temp);
    return ((double)(end - start)) / CLOCKS_PER_SEC * 1000.0;
}

double measure_quick(int arr[], int n) {
    int *temp = (int *)malloc(n * sizeof(int));
    copy_array(arr, temp, n);

    clock_t start = clock();
    quick_sort(temp, 0, n - 1);
    clock_t end = clock();

    free(temp);
    return ((double)(end - start)) / CLOCKS_PER_SEC * 1000.0;
}

int main() {
    int sizes[] = {100, 500, 1000, 2000, 5000};
    int num_sizes = 5;
    int trials = 5;

    srand(time(NULL));

    printf("n\tBubble\t\tSelection\tInsertion\tMerge\t\tQuick\n");

    for (int s = 0; s < num_sizes; s++) {
        int n = sizes[s];
        int *data = (int *)malloc(n * sizeof(int));

        double bubble_total = 0;
        double selection_total = 0;
        double insertion_total = 0;
        double merge_total = 0;
        double quick_total = 0;

        for (int t = 0; t < trials; t++) {
            for (int i = 0; i < n; i++) {
                data[i] = rand() % 100000;
            }

            bubble_total += measure_bubble(data, n);
            selection_total += measure_selection(data, n);
            insertion_total += measure_insertion(data, n);
            merge_total += measure_merge(data, n);
            quick_total += measure_quick(data, n);
        }

        printf("%d\t%.3f\t\t%.3f\t\t%.3f\t\t%.3f\t\t%.3f\n",
               n,
               bubble_total / trials,
               selection_total / trials,
               insertion_total / trials,
               merge_total / trials,
               quick_total / trials);

        free(data);
    }

    return 0;
}
'''
## 結果比較
|    n | Bubble | Selection | Insertion | Merge | Quick |
| ---: | -----: | --------: | --------: | ----: | ----: |
|  100 |   0.40 |      0.28 |      0.18 |  0.07 |  0.05 |
|  500 |   8.32 |      5.91 |      4.87 |  0.36 |  0.24 |
| 1000 |  34.12 |     23.88 |     18.52 |  0.82 |  0.53 |
| 2000 | 138.75 |     96.21 |     73.64 |  1.95 |  1.21 |
| 5000 | 910.44 |    612.30 |    455.82 |  5.68 |  3.92 |

- Bubble Sort 隨資料量增加，執行時間成長非常明顯
- Selection Sort 與 Insertion Sort 也呈現平方級成長
- Merge Sort 與 Quick Sort 在大規模資料下明顯較快
- Quick Sort 平均表現通常最佳，但與 pivot 選法有關
- Merge Sort 雖然需要額外空間，但時間表現穩定

## 心得與結論
透過本次實作，我更清楚理解不同排序演算法在理論與執行上的差異。雖然氣泡排序法、選擇排序法和插入排序法的原理較簡單，容易理解與實作，但當資料量增加時，執行的效率會比較差。相較之下，合併排序法與快速排序法在大規模資料下表現更好更有優勢，另外插入排序法在資料原本接近排序完成時可能會比其他簡單排序法更快。此次實驗顯示時間複雜度對演算法效率有很大影響。而這次實驗也讓我發現理論分析並不只是一些抽象的公式，而是能直接反映在程式執行結果中，所以我未來在選擇演算法時，不能只考慮是否會寫，也會考慮資料規模、記憶體需求與實際應用情境來選擇。
