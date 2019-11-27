#%%
##ItemCF算法
import math
def ItemSimilarity(train):
    C = dict()   ##同时购买的次数
    N = dict()   ##购买用户数
    for u,items in train.items():
        for i in items.keys():
            if i not in N.keys():
                N[i] = 0
            N[i] += 1
            for j in items.keys():
                if i == j:
                    continue
                if i not in C.keys():
                    C[i] = dict()
                if j not in C[i].keys():
                    C[i][j] = 0
                C[i][j] += 1   ##当用户购买了i和j，则加1
    W = dict()    ##相似分数
    for i, related_items in C.items():
        if i not in W.keys():
            W[i] = dict()
        for j,cij in related_items.items():
            W[i][j] = cij / math.sqrt(N[i]*N[j])
    return W

if __name__ == '__main__':
    Train_Data = {
                    'A':{'苹果':1,'香蕉':1,'西瓜':1},
                    'B':{'苹果':1,'西瓜':1},
                    'C':{'苹果':1,'香蕉':1,'菠萝':1},
                    'D':{'香蕉':1,'葡萄':1},
                    'E':{'葡萄':1,'菠萝':1},
                    'F':{'香蕉':1,'西瓜':1}
    }
    W = ItemSimilarity(Train_Data)
    print(W)
 

# %%  引入评分：基于余弦（Cosine-based）的相似度计算
import math
def ItemSimilarity(train):
    C = dict()   ##同时购买的次数
    N = dict()   ##购买用户数
    for u,items in train.items():
        for i in items.keys():
            if i not in N.keys():
                N[i] = 0
            N[i] += items[i]* items[i]
            for j in items.keys():
                if i == j:
                    continue
                if i not in C.keys():
                    C[i] = dict()
                if j not in C[i].keys():
                    C[i][j] = 0
                C[i][j] += items[i]*items[j]    ##当用户购买了i和j，则加评分乘积
    W = dict()    ##相似分数
    for i, related_items in C.items():
        if i not in W.keys():
            W[i] = dict()
        for j,cij in related_items.items():
            W[i][j] = cij / (math.sqrt( N[i]) *math.sqrt( N[j]) )
    return W

if __name__ == '__main__':
    Train_Data = {
                    'A':{'苹果':2,'香蕉':2,'西瓜':2},
                    'B':{'苹果':2,'西瓜':2},
                    'C':{'苹果':2,'香蕉':2,'菠萝':2},
                    'D':{'香蕉':2,'葡萄':2},
                    'E':{'葡萄':2,'菠萝':2},
                    'F':{'香蕉':2,'西瓜':2}
    }
    W = ItemSimilarity(Train_Data)
    print(W)

# %% 热门物品的惩罚
import math
def ItemSimilarity_alpha(train,alpha=0.3):
    C = dict()   ##同时购买的次数
    N = dict()   ##购买用户数
    for u,items in train.items():
        for i in items.keys():
            if i not in N.keys():
                N[i] = 0
            N[i] += 1
            for j in items.keys():
                if i == j:
                    continue
                if i not in C.keys():
                    C[i] = dict()
                if j not in C[i].keys():
                    C[i][j] = 0
                C[i][j] += 1    ##当用户购买了i和j，则加1
    W = dict()    ##相似分数
    for i, related_items in C.items():
        if i not in W.keys():
            W[i] = dict()
        for j,cij in related_items.items():
             W[i][j] = cij / (math.pow(N[i],alpha) *math.pow(N[j],1-alpha) )
    return W

if __name__ == '__main__':
    Train_Data = {
                    'A':{'苹果':1,'香蕉':1,'西瓜':1},
                    'B':{'苹果':1,'西瓜':1},
                    'C':{'苹果':1,'香蕉':1,'菠萝':1},
                    'D':{'香蕉':1,'葡萄':1},
                    'E':{'葡萄':1,'菠萝':1},
                    'F':{'香蕉':1,'西瓜':1}
    }
    W= ItemSimilarity_alpha (Train_Data)
    print(W)

# %%
import math
def ItemSimilarity_alpha(train,alpha=0.3):
    C = dict()   ##同时购买的次数
    N = dict()   ##购买用户数
    for u,items in train.items():
        for i in items.keys():
            if i not in N.keys():
                N[i] = 0
            N[i] += 1
            for j in items.keys():
                if i == j:
                    continue
                if i not in C.keys():
                    C[i] = dict()
                if j not in C[i].keys():
                    C[i][j] = 0
                C[i][j] += 1    ##当用户购买了i和j，则加1
    W = dict()    ##相似分数
    for i, related_items in C.items():
        if i not in W.keys():
            W[i] = dict()
        for j,cij in related_items.items():
             W[i][j] = cij / (math.pow(N[i],alpha) *math.pow(N[j],1-alpha) )
    return W

def Recommend(train,user_id,W,K):
    rank = dict()
    ru = train[user_id]
    for i,pi in ru.items():
        tmp=W[i]
        for j,wj in sorted(tmp.items(),key=lambda d: d[1],reverse=True)[0:K]:
            if j not in rank.keys():
                rank[j]=0
            if j in ru:      ##如果用户已购买，则不再推荐
                continue
            rank[j] += pi*wj   #待推荐的物品j与用户已购买的物品i相似，则累加上相似分数
    return rank

if __name__ == '__main__':
    Train_Data = {
                    'A':{'苹果':1,'香蕉':1,'西瓜':1},
                    'B':{'苹果':1,'西瓜':1},
                    'C':{'苹果':1,'香蕉':1,'菠萝':1},
                    'D':{'香蕉':1,'葡萄':1},
                    'E':{'葡萄':1,'菠萝':1},
                    'F':{'香蕉':1,'西瓜':1}
    }
    W= ItemSimilarity_alpha (Train_Data)
    print(Recommend(Train_Data,'C',W,3) )
