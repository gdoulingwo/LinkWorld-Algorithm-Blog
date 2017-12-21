---
title: 操作系统银行家算法与C++模拟实现
date: 2017-12-20 11:56:00
---
# 银行家算法

## 简介

　多道程序设计的引入，提高了系统资源的利用率，增强了系统的处理能力，但也可能发生一种危险情况：死锁。所谓死锁，就是指若干进程都无知地等待某一进程释放资源而处于无休止的等待状态。

  死锁避免是预防死锁的方法的一种思路，指在资源分配的过程中，使用某些方法防止系统进入不安全状态（不存在某种多个进程的排序，使每个进程都可依照此顺序顺利完成的状态）。

  著名的银行家算法就是死锁避免的一种方法。

## 思路分析

  银行家算法是避免死锁的算法。为实现银行家算法，每一个新进程在进入系统时，他必须申明在运行过程中，可能需要每种资源类型的最大单元数目，其数目不应超过系统所拥有的资源总量。当进程请求一组资源时，系统必须首先确定是否有足够的资源分配给该进程。若有，再进一步计算在将这些资源分配给进程后，是否会使系统处于不安全的状态。如果不会，才将资源分配给他，否则让进程等待。

  程序运行时，读取文件中的数据：可利用资源数Available\_list、各进程所需资源数Max\_list及已分配资源数Allocation\_list，然后根据`Need[i][j]=Max[i][j]-Allocation[i][j]`执行Set\_Need\_Available函数设置需求矩阵Need。当进程请求资源时，先检查请求资源数是否超出该进程尚需的资源的最大数量，超过则拒绝请求。然后检查系统中是否有足够的资源，没有则拒绝请求。

  如果检查通过，则进行试分配，然后开始测试（Test_Safty函数）。测试过程：先设置工作向量Work和标志Finish，接着循环检测满足分配的进程并分配资源，如果剩下的资源都不能保证满足任一其他进程的全部资源的请求，则视为不安全的分配，系统程序将拒绝请求。

## 数据结构与算法

### 1. 银行家算法中的数据结构

1. **可利用资源向量 Available**：一个含有m个元素的数组，其中每一个元素代表一类可利用的资源数目，其初始值是系统中所配置的该类全部可用资源的数目。
1. **最大需求矩阵 Max**：一个 n×m 的矩阵，定义了系统中n个进程中的每一个进程对m类资源的最大需求。
1. **分配矩阵 Allocation**：一个 n×m 的矩阵，定义系统中每一类资源当前已分配给每一进程的资源数。
1. **需求矩阵 Need**：一个 n×m 的矩阵，用以表示每一个进程尚需的各类资源数。

### 2. 银行家算法中的基本思想

设Requesti是进程Pi的请求向量，如果Requesti［j］=K，表示进程Pi需要K个Rj类型的资源。当Pi发出资源请求后，系统按下述步骤进行检查：

1. 如果Requesti［j］≤ Need［i,j］，便转向步骤2；否则认为出错，因为它所需要的资源数已超过它所宣布的最大值。
1. 如果Requesti［j］≤ Available［j］，便转向步骤(3)；否则， 表示尚无足够资源，Pi须等待。
1. 系统试探着把资源分配给进程Pi，并修改下面数据结构中的数值：

        Available［j］=Available［j］-Requesti［j］;
        Allocation[i,j]=Allocation[i,j]+Requesti[j］;
        Need［i,j］=Need［i,j］-Requesti［j］;

1. 系统执行安全性算法，检查此次资源分配后，系统是否处于安全状态。若安全，才正式将资源分配给进程Pi，以完成本次分配；否则， 将本次的试探分配作废，恢复原来的资源分配状态，让进程Pi等待。

### 3. 安全性算法

1. 设置两个向量：
    - 工作向量Work:  它表示系统可提供给进程继续运行所需的各类资源数目，它含有m个元素，在执行安全算法开始时，Work=Available;
    - Finish:  它表示系统是否有足够的资源分配给进程，使之运行完成。开始时先做Finish［i］= false; 当有足够资源分配给进程时， 再令Finish［i］= true。

1. 从进程集合中找到一个能满足下述条件的进程：
    - 步骤 1: Finish［i］= false;
    - 步骤 2: Need［i,j］≤ Work［j］； 若找到， 执行步骤 3， 否则，执行步骤 4。
    - 步骤 3: 当进程Pi获得资源后，可顺利执行，直至完成，并释放出分配给它的资源，故应执行：

            Work［j］= Work［j］+Allocation［i,j］;
            Finish［i］= true;
            go to step 2;

    - 步骤 4: 如果所有进程的Finish［i］= true 都满足， 则表示系统处于安全状态；否则，系统处于不安全状态。

## 模拟实现

### 程序同目录下的数据内容

- Available_list.txt

        3
        10 5 7

- Max_list.txt

        5
        7 5 3
        3 2 2
        9 0 2
        2 2 2
        4 3 3

- Allocation_list.txt

        0  3  0
        2  0  0
        3  0  2
        2  1  1
        0  0  2

### 银行家算法的C++模拟实现

``` c++
#include <iostream>
#include <stdio.h>
#include <windows.h>

#define MAX_PROCESS 32              //最大进程数
#define MAX_RESOURCE 64             //最大资源类别

int PROCESS_NUM;              //实际总进程数
int RESOURCE_NUM;               //实际资源类别数
int Available[MAX_RESOURCE];                 //可利用资源向量
int Max[MAX_PROCESS][MAX_RESOURCE];          //最大需求矩阵
int Allocation[MAX_PROCESS][MAX_RESOURCE];   //分配矩阵
int Need[MAX_PROCESS][MAX_RESOURCE];         //需求矩阵

int Request_PROCESS;                       //发出请求的进程
int Request_RESOURCE_NEMBER[MAX_RESOURCE];     //请求资源数

void Read_Available_list();      //读入可用资源Available
void Read_Max_list();           //读入最大需求矩阵Max
void Read_Allocation_list();    //读入已分配矩阵Allocation
void PrintInfo();               //打印各数据结构信息
void Read_Request();            //输入请求向量
void Allocate_Source();         //开始正式分配资源（修改Allocation_list.txt）
void Recover_TryAllocate();     //恢复试分配前状态
int Test_Safty();               //安全性检测
void RunBanker();               //执行银行家算法


//读入可用资源Available
void Read_Available_list()
{
    FILE *fp;
    if((fp=fopen("Available_list.txt","r"))==NULL)
    {
        cout<<"错误,文件打不开,请检查文件名"<<endl;
        exit(0);
    }
    fscanf(fp,"%d",&RESOURCE_NUM);
    int i=0;
    while(!feof(fp))
    {
        fscanf(fp,"%d",&Available[i]);
        i++;
    }
    fclose(fp);
}

//读入最大需求矩阵Max
void Read_Max_list()
{
    FILE *fp;
    if((fp=fopen("Max_list.txt","r"))==NULL)
    {
        cout<<"错误,文件打不开,请检查文件名"<<endl;
        exit(0);
    }
    fscanf(fp,"%d",&PROCESS_NUM);
    for(int i=0;i<PROCESS_NUM;i++)
        for(int j=0;j<RESOURCE_NUM;j++)
            fscanf(fp,"%d",&Max[i][j]);
    fclose(fp);
}

//读入已分配矩阵Allocation
void Read_Allocation_list(){
    FILE *fp;
    if((fp=fopen("Allocation_list.txt","r"))==NULL)
    {
        cout<<"错误,文件打不开,请检查文件名"<<endl;
        exit(0);
    }
    for(int i=0;i<PROCESS_NUM;i++)
        for(int j=0;j<RESOURCE_NUM;j++)
            fscanf(fp,"%d",&Allocation[i][j]);
    fclose(fp);
}

//设置需求矩阵Need
void Set_Need_Available()
{
    for(int i=0;i<PROCESS_NUM;i++)
        for(int j=0;j<RESOURCE_NUM;j++)
        {
            Need[i][j]=Max[i][j]-Allocation[i][j];
            Available[j]=Available[j]-Allocation[i][j];
        }
}

//打印各数据结构信息
void PrintInfo()
{
    cout<<"进程个数： "<<PROCESS_NUM<<"\t"<<"资源个数： "<<RESOURCE_NUM<<endl;
    cout<<"可用资源向量Available："<<endl;
    int i,j;
    for(i=0;i<RESOURCE_NUM;i++)
        cout<<Available[i]<<"\t";
    cout<<endl;
    cout<<"最大需求矩阵Max："<<endl;
    for(i=0;i<PROCESS_NUM;i++)
    {
        for(j=0;j<RESOURCE_NUM;j++)
            cout<<Max[i][j]<<"\t";
        cout<<endl;
    }
    cout<<"已分配矩阵Allocation："<<endl;
    for(i=0;i<PROCESS_NUM;i++)
    {
        for(j=0;j<RESOURCE_NUM;j++)
            cout<<Allocation[i][j]<<"\t";
        cout<<endl;
    }
    cout<<"需求矩阵Need："<<endl;
    for(i=0;i<PROCESS_NUM;i++)
    {
        for(j=0;j<RESOURCE_NUM;j++)
            cout<<Need[i][j]<<"\t";
        cout<<endl;
    }
}

//输入请求向量
void Read_Request()
{
    cout<<"输入发起请求的进程（0－"<<PROCESS_NUM-1<<")：";
    cin>>Request_PROCESS;

    cout<<"输入请求资源的数目：按照这样的格式输入 x x x：";
    for(int i=0; i<RESOURCE_NUM; i++)
        cin>>Request_RESOURCE_NEMBER[i];
}

//开始正式分配资源（修改Allocation_list.txt）void Allocate_Source()
{
    cout<<'\n'<<"开始给第"<<Request_PROCESS<<"个进程分配资源..."<<endl;
    FILE *fp;
    if((fp=fopen("Allocation_list.txt","w"))==NULL)
    {
        cout<<"错误,文件打不开,请检查文件名"<<endl;
        exit(0);
    }
    for(int i=0;i<PROCESS_NUM;i++)
    {
        for(int j=0;j<RESOURCE_NUM;j++)
            fprintf(fp,"%d  ",Allocation[i][j]);
        fprintf(fp,"\n");
    }
    cout<<"分配完成，已更新Allocation_list.txt"<<endl;
    fclose(fp);
}

//恢复试分配前状态
void Recover_TryAllocate()
{
    for(int i=0;i<RESOURCE_NUM;i++)
    {
        Available[i]=Available[i]+Request_RESOURCE_NEMBER[i];
        Allocation[Request_PROCESS][i]=Allocation[Request_PROCESS][i]-Request_RESOURCE_NEMBER[i];
        Need[Request_PROCESS][i]=Need[Request_PROCESS][i]+Request_RESOURCE_NEMBER[i];
    }
}

//安全性检测
//返回值：0：未通过安全性测试； 1：通过安全性测试
int Test_Safty()
{
    cout<<'\n'<<"进入安全性检测!"<<endl;
    int i,j;
    int Work[MAX_RESOURCE];   //定义工作向量
    for(i=0;i<RESOURCE_NUM;i++){
        Work[i]=Available[i];
    }
    bool Finish[MAX_PROCESS];  //定义布尔向量
    for(i=0;i<PROCESS_NUM;i++)
        Finish[i]=false;
    int safe[MAX_RESOURCE];   //用于保存安全序列

    bool found=false;   //判断在一轮查找中是否找到符合条件的进程
    int FinishCount=0;       //找到满足条件的进程i的数目
    while(FinishCount<5)
    {
        for(i=0;i<PROCESS_NUM;i++)
        {
            if(Finish[i]==true) //检查是否满足条件Finish[i]==false
                continue;
            bool hasResource=true;
            for(j=0;j<RESOURCE_NUM;j++)    //检查是否满足条件Need[i]<=Work
                if(Need[i][j]>Work[j])
                    hasResource=false;
            if(hasResource)
            {
                for(j=0;j<RESOURCE_NUM;j++)
                    Work[j]=Work[j]+Allocation[i][j];
                Finish[i]=true;
                safe[FinishCount]=i;
                FinishCount++;
                found=true;
            }
        }
        if(found)
        {
            found=false;
        } else
            break;
    }

    for(i=0;i<PROCESS_NUM;i++)   //判断是否所有进程满足Finish[i]==true
    {
        if(Finish[i]==true)
            continue;
        else
        {
            cout<<"未通过安全性测试，不分配"<<endl;
            return 0;
        }
    }
    cout<<'\n'<<"找到一个安全序列:";
    for(i=0;i<PROCESS_NUM;i++)    //打印安全序列
    {
        cout<<"P"<<safe[i];
        if(i!=PROCESS_NUM-1)
            cout<<"--";
    }

    return 1;
}

void RunBanker(){              //执行银行家算法
    cout<<endl;
    cout<<"开始执行银行家算法..."<<endl;

    for(int i=0;i<RESOURCE_NUM;i++)  //检查是否满足条件Request<=Need
        if(Request_RESOURCE_NEMBER[i]>Need[Request_PROCESS][i])
        {
            cout<<"\n第"<<Request_PROCESS<<"个进程请求资源不成功"<<endl;
            cout<<"原因：超出该进程尚需的资源的最大数量!"<<endl;
            return;
        }
    for(i=0;i<RESOURCE_NUM;i++)   //检查是否满足条件Request<=Available
        if(Request_RESOURCE_NEMBER[i]>Available[i])
        {
            cout<<"\n第"<<Request_PROCESS<<"个进程请求资源不成功"<<endl;
            cout<<"原因：系统中无足够的资源!"<<endl;
            return;
        }
        else{
            //试分配，更新各相关数据结构
            Available[i]=Available[i]-Request_RESOURCE_NEMBER[i];
            Allocation[Request_PROCESS][i]=Allocation[Request_PROCESS][i]+Request_RESOURCE_NEMBER[i];
            Need[Request_PROCESS][i]=Need[Request_PROCESS][i]-Request_RESOURCE_NEMBER[i];
        }
    cout<<endl<<"试分配完成..."<<endl;

    if(Test_Safty())    //使用安全性算法检查，若满足，则正式分配
        Allocate_Source();
    else                //否则恢复试分配前状态
        Recover_TryAllocate();
}

void main()
{
    char c;
    Read_Available_list();
    Read_Max_list();
    Read_Allocation_list();
    Set_Need_Available();
    PrintInfo();
    while(1)
    {
        Read_Request();
        RunBanker();
        cout<<"\n\n需要继续吗？（y-继续；n-终止）";
        cin>>c;
        if(c=='n')
            break;
        cout<<endl<<endl;
        PrintInfo();
    }
}
```

### 执行结果

![BankersAlgorithm1](https://gdoulingwo.github.io/LinkWorld-Algorithm-Blog/images/bankers/BankersAlgorithm1.png)

![BankersAlgorithm2](https://gdoulingwo.github.io/LinkWorld-Algorithm-Blog/images/bankers/BankersAlgorithm2.png)

## 参考资料

《计算机操作系统（第四版）》

[银行家算法实验](https://wenku.baidu.com/view/7a4d732ce2bd960590c677a7.html)
