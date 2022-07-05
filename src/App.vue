<template>
  <div>
    <input
      type="text"
      placeholder="搜索-书本名称"
      v-model="bookname"
      @keydown.enter="findBook"
    />
    <div>
      <table border="1" cellpadding="10" cellspacing="0">
        <tr>
          <th>序号</th>
          <th>书名</th>
          <th>作者</th>
          <th>出版社</th>
          <th>操作</th>
        </tr>
        <tr v-for="item in list" :key="item.id">
          <td>{{ item.id }}</td>
          <td>{{ item.bookname }}</td>
          <td>{{ item.author }}</td>
          <td>{{ item.publisher }}</td>
          <td>
            <button @click="delFn">删除</button>
            <button>详情</button>
          </td>
        </tr>
      </table>
    </div>
    <div>
      <input type="text" placeholder="书名" v-model="bookObj.bookname" /><br />
      <input type="text" placeholder="作者" v-model="bookObj.author" /><br />
      <input type="text" placeholder="出版社" v-model="bookObj.publisher" />
    </div>
    <button @click="addFn">新增</button>
    <td>
      <tr></tr>
    </td>
  </div>
</template>

<script>
// 目标1: 获取所有图书信息
// 1. 下载axios yarn add axios/ npm i axios/ cnpm i axios
// 2. 引入axios 做接口请求

// 3. 发起axios请求
// 4. 全局配置
export default {
  data() {
    return {
      list: [],
      bookname: '',
      bookObj: {
        // 参数名提前和后台的参数名对上-发送请求就不用再次对接了
        bookname: '',
        author: '',
        publisher: '',
      },
    };
  },
  mounted() {
    this.$axios({
      url: '/api/getbooks',
      method: 'GET', // 默认就是GET方式请求, 可以省略不写
    }).then((res) => {
      // console.log(res);
      const { data } = res;
      this.list = data.data;
    });
  },
  methods: {
    findBook() {
      if (this.bookname == '') {
        return alert('Please enter a bookname');
      }
      this.$axios({
        url: '/api/getbooks',
        method: 'GET',
        params: {
          // 都会axios最终拼接到url?后面
          bookname: this.bookname,
        },
      }).then((res) => {
        const { data } = res;
        this.list = data.data;
        console.log(this.list);
      });
    },
    addFn() {
      this.$axios({
        url: '/api/addbook',
        method: 'POST',
        data: {
          appkey: '7250d3eb-18e1-41bc-8bb2-11483665535a',
          ...this.bookObj,
        },
      }).then((res) => {
        console.log(res);
        location.replace('http://localhost:8080/');
      });
    },
    delFn(id) {
      this.$axios({
        url: '/api/delbook',
        params: { id },
      }).then(() => {
        this.$axios({
          url: '/api/getbooks',
          method: 'GET',
          params: {
            // 都会axios最终拼接到url?后面
            bookname: this.bookname,
          },
        });
      });
    },
  },
};
</script>

<style>
input {
  font-size: 20px;
  height: 35px;
  border-radius: 10px;
  border: 1px solid #c8cccf;
  color: #6a6f77;
  margin-bottom: 10px;
  margin-top: 5px;
}
</style>
