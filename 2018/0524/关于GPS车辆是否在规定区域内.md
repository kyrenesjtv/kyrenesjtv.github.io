## 在工作中突然接到这样子一个需求，要做一个关于车辆GPS报警。

### 具体需求大概是这个样子的：在地图上画一个多边形，保存其每个的点经纬度，当有车辆出入的时候(从后台回去到车辆的当前经纬度)，进行一个驶出或者驶入报警。


- 下面这个是射线的方式
```
public class SystemTask {


    double INFINITY = 1e10;//e代表次方  在这里表示最大正数

    double ESP = 1e-5;//最小正数

    int MAX_N = 1000;



    List<Point> Polygon;

    // 计算叉乘 |P0P1| × |P0P2|

    double Multiply(Point p1, Point p2, Point p0){
        return ( (p1.x - p0.x) * (p2.y - p0.y) - (p2.x - p0.x) * (p1.y - p0.y) );
    }


    // 判断线段是否包含点point

    private boolean IsOnline(Point point, LineSegment line)
    {
        return( ( Math.abs(Multiply(line.pt1, line.pt2, point)) < ESP ) &&

                ( ( point.x - line.pt1.x ) * ( point.x - line.pt2.x ) <= 0 ) &&

                ( ( point.y - line.pt1.y ) * ( point.y - line.pt2.y ) <= 0 ) );
    }

    // 判断线段相交
    private boolean Intersect(LineSegment L1, LineSegment L2)
    {
        return( (Math.max(L1.pt1.x, L1.pt2.x) >= Math.min(L2.pt1.x, L2.pt2.x)) &&

                (Math.max(L2.pt1.x, L2.pt2.x) >= Math.min(L1.pt1.x, L1.pt2.x)) &&

                (Math.max(L1.pt1.y, L1.pt2.y) >= Math.min(L2.pt1.y, L2.pt2.y)) &&

                (Math.max(L2.pt1.y, L2.pt2.y) >= Math.min(L1.pt1.y, L1.pt2.y)) &&

                (Multiply(L2.pt1, L1.pt2, L1.pt1) * Multiply(L1.pt2, L2.pt2, L1.pt1) >= 0) &&

                (Multiply(L1.pt1, L2.pt2, L2.pt1) * Multiply(L2.pt2, L1.pt2, L2.pt1) >= 0)

        );
    }

	/* 射线法判断点q与多边形polygon的位置关系，要求polygon为简单多边形，顶点逆时针排列

	如果点在多边形内： 返回0

	如果点在多边形边上： 返回1

	如果点在多边形外： 返回2

	*/

    public int InPolygon(List<Point> polygon, Point point)

    {
        int n = polygon.size();
        int count = 0;
        LineSegment line = new LineSegment();

        line.pt1 = point;
        line.pt2.y = point.y;
        line.pt2.x = - INFINITY;
        for( int i = 0; i < n; i++ ) {

            // 得到多边形的一条边

            LineSegment side = new LineSegment();

            side.pt1 = polygon.get(i);

            side.pt2 = polygon.get((i + 1) % n);

            if( IsOnline(point, side) ) {

                return 1 ;

            }

            // 如果side平行x轴则不作考虑

            if( Math.abs(side.pt1.y - side.pt2.y) < ESP ) {

                continue;

            }

            if( IsOnline(side.pt1, line) ) {

                if( side.pt1.y > side.pt2.y ) count++;

            } else if( IsOnline(side.pt2, line) ) {

                if( side.pt2.y > side.pt1.y ) count++;

            } else if( Intersect(line, side) ) {

                count++;

            }
        }

        if ( count % 2 == 1 )
        {
            return 0;
        }

        else
        {
            return 2;
        }

    }

    public static void main(String[] args)
    {
        SystemTask systemTaskJob = new SystemTask();
        List<Point> polygon = new ArrayList<Point>();
        Point point1 = new Point(4,9);
        Point point2 = new Point(7,10);
        Point point3 = new Point(8,2);
        Point point4 = new Point(6,8);

        Point checkpoint = new Point(6,9);
        polygon.add(point1);
        polygon.add(point2);
        polygon.add(point3);
        polygon.add(point4);

        int m = systemTaskJob.InPolygon(polygon, checkpoint);
        System.out.println("========="+m);
    }

}

 class Point
{
    public double x;
    public double y;
    public Point()
    {}
    public Point(double x,double y)
    {
        this.x=x;
        this.y=y;
    }
}

class LineSegment
{
    public Point pt1;
    public Point pt2;
    public LineSegment()
    {
        this.pt1 = new Point();
        this.pt2 = new Point();
    }
}
```