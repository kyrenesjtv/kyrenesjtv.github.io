### 题目如下图1所示：从s出发到g最小的weight是多少

- ![图1](路线图.jpg)

### 我们先将跟s连接的点房屋mw中，然后从mw中取出最小值，放入resut_mw中，再从mw中删除最小值。然后将连接最小值的点放入mw中，在判断mw，如此循环，直到mw中没有数据。详见图2

- ![图2](流程图.jpg)


#### 实体类
~~~
package me.kyrene.demo.test;

import java.util.List;
import java.util.Map;

/**
 * @ProjectName: demo4Java
 * @Author: AlbertW
 * @CreateDate: 2019/1/24 16:36
 */
public class DijkNode {

    //当前节点的名字
    public String lable;

    //子节点
    public List<DijkNode> children ;

    //子节点对应的重量
    public Map<String,Double> weights;


    public DijkNode(String lable) {
        this.lable = lable;
    }
}

~~~

~~~
    /**
     *  初始化数据
     * @return 数据源
     */
    public static DijkNode initData(){
        DijkNode start = new DijkNode("s");
        DijkNode a = new DijkNode("a");
        DijkNode b = new DijkNode("b");
        DijkNode c = new DijkNode("c");
        DijkNode d = new DijkNode("d");
        DijkNode e = new DijkNode("e");
        DijkNode f = new DijkNode("f");
        DijkNode g = new DijkNode("g");
        DijkNode h = new DijkNode("h");

        List<DijkNode> children = new ArrayList<>();
        children.add(a);
        children.add(b);
        children.add(c);
        children.add(d);
        Map<String, Double> weights = new HashMap<>();
        weights.put("a", 0.5);
        weights.put("b", 0.3);
        weights.put("c", 0.2);
        weights.put("d", 0.4);
        start.children = children;
        start.weights = weights;

        children = new ArrayList<>();
        children.add(e);
        weights = new HashMap<>();
        weights.put("e", 0.3);
        a.children = children;
        a.weights = weights;

        children = new ArrayList<>();
        children.add(a);
        children.add(f);
        weights = new HashMap<>();
        weights.put("a", 0.2);
        weights.put("f", 0.1);
        b.children = children;
        b.weights = weights;

        children = new ArrayList<>();
        children.add(f);
        children.add(h);
        weights = new HashMap<>();
        weights.put("f", 0.4);
        weights.put("h", 0.8);
        c.children = children;
        c.weights = weights;

        children = new ArrayList<>();
        children.add(c);
        children.add(h);
        weights = new HashMap<>();
        weights.put("c", 0.1);
        weights.put("h", 0.6);
        d.children = children;
        d.weights = weights;

        children = new ArrayList<>();
        children.add(g);
        weights = new HashMap<>();
        weights.put("g", 0.1);
        e.children = children;
        e.weights = weights;

        children = new ArrayList<>();
        children.add(e);
        children.add(h);
        weights = new HashMap<>();
        weights.put("e", 0.1);
        weights.put("h", 0.2);
        f.children = children;
        f.weights = weights;

        children = new ArrayList<>();
        children.add(g);
        weights = new HashMap<>();
        weights.put("g", 0.4);
        h.children = children;
        h.weights = weights;

        return start;
    }

    /**
     *  获取最小权重
     * @param mw 未剔除的节点集
     * @return 最小节点
     */
    public static String findGeoWithMinWeight(Map<String,Double> mw){
        double min = Double.MAX_VALUE;
        String label = "";
        for(Map.Entry<String, Double> entry : mw.entrySet()){
            if(entry.getValue() < min){
                min = entry.getValue();
                label = entry.getKey();
            }
        }
        return label;
    }

    /**
     * 更新最小权重
     * @param key 当前节点名称
     * @param value 当前节点到初始节点的加起来的值
     * @param result_mw 每个点到初始节点的值
     */
    public static void updateWeight(String key, Double value, Map<String, Double> result_mw) {
        if(result_mw.containsKey(key)){
            //判断value大小
            if(value<result_mw.get(key)){
                result_mw.put(key,value);
            }
        }else {
            result_mw.put(key,value);
        }
    }

    /**
     *  根据label获取到最小节点
     * @param l 访问过的节点
     * @param label 节点名称
     * @return DijkNode
     */
    public static DijkNode getMinDijkNode(List<DijkNode> l , String label){

        if(l != null && l.size() >0){
            for(DijkNode d : l){
                if(d.lable.equals(label)){
                    return d;
                }
            }
        }
        return null;
    }


        public static Map<String, Double> dijkstraArithmetic(DijkNode start , Map<String, Double> mw ,Map<String, Double> result_mw){

            //子节点
            List<DijkNode> children = start.children;
            //子节点的weight
            Map<String, Double> weights = start.weights;

            //子节点放入
            for(Map.Entry<String, Double> entry :weights.entrySet()){
                mw.put(entry.getKey(),entry.getValue());
            }

            while(mw.size() != 0){
                String minLabel = findGeoWithMinWeight(mw);
                //在result_mw更新minLabel
                updateWeight(minLabel,mw.get(minLabel),result_mw);
                DijkNode minDijkNode = getMinDijkNode(children, minLabel);
                List<DijkNode> minChildrens = minDijkNode.children;
                //更新最小节点下一级节点到起点的weight
                if(minChildrens != null && minChildrens.size() >0){
                    children.addAll(minChildrens);
                    for(DijkNode minNode : minChildrens){
                        //bigDecimal 控制精度
                        mw.put(minNode.lable,BigDecimal.valueOf(minDijkNode.weights.get(minNode.lable)).add(BigDecimal.valueOf(result_mw.get(minLabel))).doubleValue());
                    }
                }
                mw.remove(minLabel);
            }
            return result_mw;
        }

~~~
