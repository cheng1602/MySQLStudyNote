# 数据库兼容经验

Oracle 迁移至 MySQL

- 连接字符串 "||"

  `'%' || #{page.name} || '%'`

  `concat('%', #{page.name}, '%')`

  

- decode函数

  `decode(条件, 值1, 返回值1, 值2, 缺省值)`

  `(case 条件 when 值1 then 返回值1 when 值2 then 返回值2 else 缺省值 end)`

  

- order by关键字

  默认条件下MySQL的设置为`ONLY_FULL_GROUP_BY`，取消这一配置即可

  ```sql
  select @@sql_mode
  -- 默认条件下的查询结果 'ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
  ```

  ```sql
  set @@sql_mode 
  ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
  ```



- rownum

  ```sql
  select * from (select @rownum:=@rownum+1 rownum, tt.* from td_ec_share_up_lic_data tt, (SELECT @rownum:=0) r)t
  		    where t.is_system = '1'
  			and t.cd_operation !='D'
  			and t.system_id is null
  			and substr(t.certificateareacode, 1, 4) = #{areaCode,jdbcType=VARCHAR}
  			and rownum &lt; #{maxNumb}
  ```

  ```sql
  select * from (select @rownum:=@rownum+1 rownum, tt.* from td_ec_share_up_lic_data tt, (SELECT @rownum:=0) r)t
  			where t.is_system = '1'
  			and t.cd_operation !='D'
  			and t.system_id = #{provincial_tyshxydm,jdbcType=VARCHAR}
  			and rownum &lt; #{maxNumb}
  ```



- to_date函数

  `to_date(#{time},'yyyy-mm-dd hh24:mi:ss')`

  `str_to_date(#{time},'%Y-%m-%d %T')`



- to_char函数

  `to_char(#{time}, 'yyyy-MM-dd')`

  `date_format(#{time},'%Y%m%d')`



- substr函数

  Oracle中可以从0位开始截取

  MySQL中必须从大于等于1位开始截取，从0位开始无法截取出数据
