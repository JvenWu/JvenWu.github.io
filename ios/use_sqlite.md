## for IOS: How to use SQLite database | SQLite数据库
```objectivec
//SQLite 官方文档 https://www.sqlite.org
/*
SQLite存储类型
NULL : 空值
INTEGER : 整型值
REAL : 浮点值
TEXT : 文本字符串
BLOB : 二进制数据（比如文件）
*/

@interface ViewController ()
{
    sqlite3 *_db;  //数据库
}
- (IBAction)insertData;
- (IBAction)deleteData;
- (IBAction)updateData;
- (IBAction)queryData;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    //获取沙盒中的数据库文件名
    NSString * fileName=[[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES)
        lastObject] stringByAppendingPathComponent:@"Fugitive.sqlite"];
    NSLog(@"fileName = %@",fileName);
    //创建或打开数据库，如果数据库文件不存在会自动创建
    int result=sqlite3_open(fileName.UTF8String, &_db);
    if (result==SQLITE_OK) {
        NSLog(@"成功打开数据库");
        //创建表
        const charchar *sql = "create table if not exists t_Fugitive (id INTEGER primary key autoincrement,
            name TEXT, desc TEXT, bounty REAL);";
        charchar *errorMessage=NULL;
        int result=sqlite3_exec(_db, sql, NULL, NULL, &errorMessage);
        if (result==SQLITE_OK) {
            NSLog(@"成功创建表");
        }else{
            NSLog(@"创建表失败: %s",errorMessage);
        }
    }else{
        NSLog(@"打开数据库失败");
    }
}

/**
 *  插入数据
 */
- (IBAction)insertData {
    for (int i=0; i<5; i++) {
        NSString *name =[NSString stringWithFormat:@"Fugitive－%d",i];
        int bounty = i;
        NSString *sql = [NSString stringWithFormat:@"insert into t_Fugitive (name, bounty) values('%@', %d);",
            name, bounty];
        charchar *errorMessage=NULL;
        int result=sqlite3_exec(_db, sql.UTF8String, NULL, NULL, &errorMessage);
        if (result==SQLITE_OK) {
            NSLog(@"成功添加数据");
        }else{
            NSLog(@"添加数据失败:%s",errorMessage);
        }
    }
}

/**
 *  删除数据
 */
- (IBAction)deleteData {
    const charchar *sql = "delete from t_Fugitive where id = 3;";
    charchar *errorMessage=NULL;
    int result=sqlite3_exec(_db, sql.UTF8String, NULL, NULL, &errorMessage);
    if (result==SQLITE_OK) {
        NSLog(@"删除成功！");
    }else{
        NSLog(@"删除失败！");
    }
}

/**
 *  更新数据
 */
- (IBAction)updateData {
    const charchar *sql = "update t_Fugitive set name = ‘abc’, bounty = 20 ;";
    charchar *errorMessage=NULL;
    int result=sqlite3_exec(_db, sql.UTF8String, NULL, NULL, &errorMessage);
    if (result==SQLITE_OK) {
        NSLog(@"更新成功！");
    }else{
        NSLog(@"更新失败！");
    }
}

/**
 *  查询数据
 */
- (IBAction)queryData {
    const charchar *sql = "select id, name, age from t_Fugitive where name = ?;";
    //定义一个stmt存放结果集
    sqlite3_stmt *stmt=NULL;
    //检测SQL语句的合法性
    int result =sqlite3_prepare_v2(_db, sql, -1, &stmt, NULL);
    if (result ==SQLITE_OK) {
        NSLog(@"查询语句合法");
        //执行SQL语句，从结果集中取出数据
        while (sqlite3_step(stmt)==SQLITE_ROW) { //获得这行对应的数据
            //获得第0列的id
            int sid=sqlite3_column_int(stmt, 0);
            //获得第1列的name
            const unsigned charchar * sname=sqlite3_column_text(stmt, 1);
            NSLog(@"%d %s %d",sid,sname);
        }
    }else{
        NSLog(@"查询语句非法");
    }
}
@end
```
