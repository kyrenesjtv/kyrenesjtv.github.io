#### 给定m个元素，取出个数为n个的不重复组合

~~~
import java.util.ArrayList;
import java.util.Arrays;

public class Lesson8_1 {

	/**
    * @Description:	使用函数的递归（嵌套）调用，找出所有可能的队伍组合
    * @param teams- 目前还剩多少队伍没有参与组合，result- 保存当前已经组合的队伍
    * @return void
    */

    public static void combine(ArrayList<String> teams, ArrayList<String> result, int m) {

    	// 挑选完了 m 个元素，输出结果
    	if (result.size() == m) {
    		System.out.println(result);
    		return;
    	}

    	for (int i = 0; i < teams.size(); i++) {
    		// 从剩下的队伍中，选择一队，加入结果
    		ArrayList<String> newResult = (ArrayList<String>)(result.clone());
    		newResult.add(teams.get(i));

    		// 只考虑当前选择之后的所有队伍
    		ArrayList<String> rest_teams = new ArrayList<String>(teams.subList(i + 1, teams.size()));//包头不包尾

    		// 递归调用，对于剩余的队伍继续生成组合
    		combine(rest_teams, newResult, m);
    	}

    }

}


~~~


#### 给定m个元素，取出个数为n个的不重复组合total数

~~~
    /**
     * 求组合总数
     * @param source n个元素
     * @param num 取m个
     */
    public static int combinationTotal(int source , int num ){
        if(num > source){
            return 0;
        }
        int index = source - num ;
        int result = 1;
        while(source > index){
            result *= source--;
        }
        //剔除重复排列的
        while(num > 1 ){
            result /= num--;
        }
        return result;
    }

    public static void winCombination(){

    }
~~~