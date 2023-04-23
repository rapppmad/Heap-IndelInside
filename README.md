# Heap-IndelInside
import java.util.ArrayList;
import java.util.Scanner;

//1. add (nama-proses)
//  Terjadi ketika menambahkan proses baru ke priority queue

//2. executing (nama-proses)
//  Terjadi ketika proses mengeksekusi proses baru (setelah berpindah proses)

//3. (nama-proses) done
//  Terjadi ketika proses yang dieksekusi telah selesai (burst-time habis)

//4. (nama-proses) preempted
//  Terjadi ketika terdapat proses yang memiliki priority lebih tinggi dari proses yang sedang dieksekusi

public class Solution {
    public static void main(String[] args) {
        Scanner get = new Scanner(System.in);
        Process indel;
        indel = new Process();

        int n = get.nextInt();get.nextLine();
        for (int i = 0; i < n; i++) {
            String[] inp = get.nextLine().split(" ");
            Node node = new Node(inp[0], Integer.parseInt(inp[1]), Integer.parseInt(inp[2]), Integer.parseInt(inp[3]));
            indel.addWaitGroup(node);
        }
        indel.startProcess();
        get.close();
    }
}

class Process {
    Node task;
    int timer = 0;
    boolean finish = false;
    ArrayList<Node> waitGroup;
    Heap heap;
    
    public Process(){
        waitGroup = new ArrayList<>();
        heap = new Heap<>();
    }
    public ArrayList<Node> getWaitGroup() {
        return waitGroup;
    }
    public void setWaitGroup(ArrayList<Node> waitGroup) {
        this.waitGroup = waitGroup;
    }
    public Heap getHeap() {
        return heap;
    }
    public void setHeap(Heap heap) {
        this.heap = heap;
    }
    private void addHeap(Node task) {
        heap.insert(task);
        System.out.printf("%03d add %s\n", timer, task.task);
    }
    // masukkan proses ke waitQueue
    public void addWaitGroup(Node node) {
        waitGroup.add(node);
    }
    private boolean endProcess() {
        return waitGroup.isEmpty() && heap.arrayKosong();
    }
    private void waitedTask() {
        for (int i = 0; i < waitGroup.size(); i++) {
            if (waitGroup.get(i).arrivalTime <= timer) {
                addHeap(waitGroup.get(i));
                waitGroup.remove(i);
                i--;
            }
        }
    }
    private void eksekusi() {
        if (timer == 0 || heap.arrayKosong()) {
            return;
        }
        Node topTask = heap.getTop();
        if (task != topTask) {
            if (task != null && !finish) {
        System.out.print(new StringBuilder().append(String.format("%03d %s preempted\n", timer, task.task)));
            }
        System.out.print(new StringBuilder().append(String.format("%03d executing %s\n", timer, heap.getTop().task)));
            task = topTask;
        }
        topTask.burstTime--;
        finish = false;
        if (topTask.burstTime == 0) {
            System.out.printf("%03d %s done\n", timer, topTask.task);
            heap.delete();
            finish = true;
        }
    }
    public void startProcess() {
        while(!endProcess()) {
            waitedTask();
            eksekusi();
            ++timer;
        }
    }
}

class Node {
    String task;
    int priority,burstTime,arrivalTime;
    
    public Node(){
    }
    public Node(String task){
        this.task = task;
    }
    public Node(String task, int priority, int burstTime, int arrivalTime) {
        this.task = task;this.priority = priority;
        this.burstTime = burstTime;this.arrivalTime = arrivalTime;
    }
}

class Heap<T extends Comparable<T>> {
    private ArrayList<Node> processHeap;
    public Heap(int size) {
        processHeap = new ArrayList<>(size);
    }
    public Heap(){
        processHeap = new ArrayList<>();
    }
    public int getSize() {
        return processHeap.size();
    }
    public boolean arrayKosong() {
        return processHeap.isEmpty();
    }
    // masukkan data ke paling belakang, lalu heapifyUp
    public void insert(Node node) {
        processHeap.add(node);
        heapifyUp(processHeap.size() - 1);
    }
    // kembalikan data pertama (datas[0])
    public Node getTop() {
        if (arrayKosong()) return null;
        return processHeap.get(0);
    }
    // hapus data paling awal, lalu heapifyDown
    public Node delete() {
        if (processHeap.isEmpty()) return null;
        Node root = processHeap.get(0);
        processHeap.set(0, processHeap.get(processHeap.size() - 1));
        processHeap.remove(processHeap.size() - 1);
        heapifyDown(0);
        return root;
    }
    //Untuk mendapatkan yang terkecil
    private boolean getNodeTerkecil(int index1, int index2) {
        Node x1 = processHeap.get(index1);
        Node x2 = processHeap.get(index2);
        return (x1.priority < x2.priority) ||
                (x1.priority == x2.priority && x1.arrivalTime < x2.arrivalTime);
    }
    private void heapifyUp(int currentIndex) {
        if (currentIndex == 0) {
            return;
        }
        int parent = (currentIndex - 1) / 2;
        if (currentIndex>0 && processHeap.get(parent).priority > processHeap.get(currentIndex).priority) {
            swap(currentIndex, parent);
            heapifyUp(parent);
        }
    }
    private void heapifyDown(int currentIndex) {
        int left= 2 * currentIndex  + 1;
        int right = 2 * currentIndex + 2;
        int smallest = currentIndex;

        if (left < processHeap.size() && getNodeTerkecil(left, smallest)) {
            smallest = left;
        }
        if ( right < processHeap.size() && getNodeTerkecil(right, smallest)) {
            smallest = right;
        }

        if (smallest != currentIndex) {
            swap(currentIndex, smallest);
            heapifyDown(smallest);
        }
    }
    private void swap(int index1, int index2) {
        Node temp = processHeap.get(index1);
        processHeap.set(index1, processHeap.get(index2));
        processHeap.set(index2, temp);
    }
}
