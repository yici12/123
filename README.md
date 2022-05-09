1.主函数：导入数据集，划分训练集与测试集，构造决策树 Main_DecisionTree.m
2.子函数ID3()：基于ID3算法构造决策树 ID3.m
3.子函数chooseFeature()：选择信息增益最大的属性特征 chooseFeature.m


function bestFeature=chooseFeature(dataset,~)
% 选择信息增益最大的属性特征
 %数据预处理
[N,M]=size(dataset);                %样本数量N
M=M-1;                              %特征个数M
y=strcmp(dataset(:,M+1),dataset(1,M+1)); %标签y(以第一个标签为1)
x=dataset(:,1:M);                   %数据x
gain = (1:M);                       %创建一个数组，用于储存每个特征的信息增益
%bestFeature;                       %最大信息增益的特征
Ent_D=calShannonEnt(y);             %当前信息熵
%计算信息增益
for i=1:M
    % 计算第i种属性的增益
    temp=tabulate(x(:,i));
    value=temp(:,1);            %属性值
    count=cell2mat(temp(:,2));  %不同属性值的各自数量
    Kind_Num=length(value);     %取值数目
    Ent=zeros(Kind_Num,1);
    % i属性下 j取值的信息熵
    for j=1:Kind_Num
        % 在第j种取值下正例的数目
        Ent(j)= calShannonEnt( y(strcmp(x(:,i),value(j))) );
    end
    gain(i)=Ent_D-count'/N*Ent;
end
%随机挑选一个最大值
max_gain=find(gain==max(gain));
choose=randi(length(max_gain));
bestFeature=max_gain(choose);
%%%%============================================
end


1）对数据进行预处理
这里对数据的标签进行了特殊处理
y=strcmp(dataset(:,M+1),dataset(1,M+1)); %标签y(以第一个标签为1)
因为是二分类问题，所以将标签调整的为logical数组，与第一个相同的为1.不相同的为0，方便后续的计算信息熵的处理。
（2）计算信息熵
先利用tabulate来获取每个属性下的不同取值以及其数量


   temp=tabulate(x(:,i));
   value=temp(:,1);            %属性值
   count=cell2mat(temp(:,2));  %不同属性值的各自数量

这里以此对每一个属性取值调用calShannonEnt函数来计算信息熵

Ent(j)= calShannonEnt( y(strcmp(x(:,i),value(j))) );

这里y(strcmp(x(:,i),value(j)))是选取了和x(:,i)和value(j)相同的取值对应的y传递给calShannonEnt去计算信息熵
(3）随机选取最大的信息熵
max_gain=find(gain==max(gain));
choose=randi(length(max_gain));
bestFeature=max_gain(choose);
这是为了应对当有多个最大的属性特征时的情况，这里我解决的方法是随机从中随机挑选出一个作为最大值，所以也因此，虽然输入数据相同，但训练输出的树也会因此而可能发生不同。
4.子函数calShannonEnt()：计算信息熵 calShannonEnt.m
function shannonEnt = calShannonEnt(y)
% 计算信息熵
% y对应的标签,为1或0，对应正例与反例
    N=length(y);            %标签长度
    P_T=sum(y)/N;           %正例概率
    P_F=(N-sum(y))/N;         %反例概率
    if(P_T==0||P_F==0)
        %使得p*log2p为0
        shannonEnt = 0; 
        return
    end
    shannonEnt=-(P_T*log2(P_T)+P_F*log2(P_F));  %信息熵
end
因为传入的为[1，0]的逻辑数组，所以正例可以直接使用sum/N来计算。后面根据信息熵的约定当p=0信息熵p*log2p为0，所以加入了中间的一句判断，来返回该情况

5.子函数splitDataset()：划分数据集 splitDataset.m
6.子函数print_tree()：遍历决策树 print_tree.m
7.子函数tree_plot()：绘出决策树tree_plot.m
8.利用子函数predict()对测试样本进行分类predict.m文件
对训练样本进行类别划分的代码如下：
%----------------------------------------------
y_test=predict(x_test,mytree,labels);
fprintf('测试样本的分类标签为：');
disp(y_test);
%-----------------------------------------------



           




                                          小组成员：连雪柯，陈艾蝶，于洁，韩泽宇
