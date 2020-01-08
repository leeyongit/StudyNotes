## JPA对象注解类型

@Table - 映射表名
@Id - 主键
@GeneratedValue(strategy=GenerationType.IDENTITY) - 自动递增生成
@Column(name = “dict_name”,columnDefinition=”varchar(100) COMMENT ‘字典名’”) - 字段名、类型、注释
@UpdateTimestamp - 更新时自动更新时间
@CreationTimestamp - 创建时自动更新时间
@Version - 版本号，更新时自动加1

## 代码示例

```java
package com.weidian.decorate.dao.domain;

import java.sql.Timestamp;

import javax.persistence.Basic;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.persistence.Version;

import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

@Entity
@Table(name = “sys_dictionary”)
public class SysDictionary {
    private long id;
    private String dictName;
    private String dictCode;
    private String dictValue;
    private Timestamp updateTime;
    private Timestamp addTime;
    private long version;

    @Id
    @Column(name = "id",columnDefinition="bigint COMMENT '主键，自动生成'")
    @GeneratedValue(strategy=GenerationType.IDENTITY)

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    @Basic
    @Column(name = "dict_name",columnDefinition="varchar(100) COMMENT '字典名'")
    public String getDictName() {
        return dictName;
    }

    public void setDictName(String dictName) {
        this.dictName = dictName;
    }

    @Basic
    @Column(name = "dict_code",columnDefinition="varchar(100) COMMENT '字典名编码'")
    public String getDictCode() {
        return dictCode;
    }

    public void setDictCode(String dictCode) {
        this.dictCode = dictCode;
    }

    @Basic
    @Column(name = "dict_value",columnDefinition="varchar(100) COMMENT '字典值'")
    public String getDictValue() {
        return dictValue;
    }

    public void setDictValue(String dictValue) {
        this.dictValue = dictValue;
    }

    @UpdateTimestamp
    @Column(name = "update_time",columnDefinition="DATETIME COMMENT '最后更新时间'")
    public Timestamp getUpdateTime() {
        return updateTime;
    }

    public void setUpdateTime(Timestamp updateTime) {
        this.updateTime = updateTime;
    }

    @CreationTimestamp
    @Column(name = "add_time",columnDefinition="DATETIME COMMENT '添加时间'")
    public Timestamp getAddTime() {
        return addTime;
    }

    public void setAddTime(Timestamp addTime) {
        this.addTime = addTime;
    }

    @Version
    @Column(name = "version",columnDefinition="bigint COMMENT '版本号'")
    public long getVersion() {
        return version;
    }

    public void setVersion(long version) {
        this.version = version;
    }

}

```

