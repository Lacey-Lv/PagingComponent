<template>
    <div class="pagination">
        <div class="page_left">
            <span v-show="siShowTotalCount" :style="{color: fontColor}">共{{ total }}条</span>
            <span v-show="isShowCon">，每页显示&nbsp;&nbsp;</span>
            <select id="mySelect" v-show="isShowCon" v-model="currSize" @change="pageSizeChange" @click="PageSizeClick">
                <option v-for="val in pageSizes" :value="val">{{ val }}</option>
            </select>
            <i class="iconfont icon-xiala" v-show="isShow"></i>
            <i class="iconfont icon-xiala-copy" v-show="!isShow"></i>
            <span v-show="isShowCon">&nbsp;&nbsp;条</span>
        </div>
        <div class="page_center"></div>
        <div class="page_right">
            <span class="comm_span" @click="changePage(1, 0)" style="display: false" v-show="isShowCon">首页</span>
            <span class="comm_span" @click="prePage()">上页</span>
            <ul>
                <li v-for="(item, index) in currShowPage" :key="index" @click="changePage(item, index)" v-bind:class="{li_curr:item === currPage}">{{ item }}</li>
            </ul>
            <span class="comm_span" @click="afterPage()">下页</span>
            <span class="comm_span" @click="changePage(lastPage, 0)" v-show="isShowCon">尾页</span>
            <input class="page_input" placeholder="页数" v-model="inputPage" v-show="isShowCon" /><i @click="GoPage()">Go</i>
        </div>
    </div>
</template>
<script>
export default {
    props: {
        /** 总条数,若显示总条数的话，必传 */
        total: {
            type: Number,
            default() {
                return 0;
            }
        },
        /** 每页显示条数数组，默认每页可显示10,8,6，非必传 */
        pageSizes: {
            type: Array,
            default() {
                return [10, 8, 6];
            }
        },
        /** 控制每页显示条数，首页，尾页，跳转到第几页 等的显示隐藏，有些不需要显示这些信息的页面，可选择隐藏掉，非必传 */
        isShowCon: {
            type: Boolean,
            default() {
                return true;
            }
        },
        /** 控制总条数的显示隐藏，非必传 */
        siShowTotalCount: {
            type: Boolean,
            default() {
                return true;
            }
        },
        /** 控制共多少条数的字体颜色，方便教师/领导等页面设置样式 */
        fontColor: {
            type: String,
            default() {
                return '#4D4D4D';
            }
        },
        /** 控制当页数大于7的时候，中间块显示的个数，为了方便教师/领导页面设置, 不能大于5 */
        blockNum: {
            type: Number,
            default() {
                return 5;
            }
        }
    },
    data() {
        return {
            currSize: 0,
            currPage: 1,
            currShowPage: [],
            inputPage: 0,
            allCount: 0,
            isShow: true
        };
    },
    watch: {
        total: function() {
            this.getAllPage();
        }
    },
    methods: {
        /** 切换页面 */
        changePage(val, currIndex) {
            // let middleNum = Math.ceil(this.allCount / 2);
            if (val === '...') { // 若点击的是省略号，则展开或者折起相应的页面
                if (currIndex < 2) { // 点击前面的【...】
                    if (this.currShowPage[currIndex + 1] > this.blockNum + 1) {
                        let currNum = this.currShowPage[currIndex + 1];
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = currNum - this.blockNum; i < currNum; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    } else {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        for (let i = 1; i <= this.blockNum; i++) {
                            this.currShowPage.push(i + 1);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    }
                } else { // 点击后面的【...】
                    if (this.currShowPage[currIndex - 1] < this.allCount - this.blockNum) {
                        let currNum = this.currShowPage[currIndex - 1];
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = currNum + 1; i <= currNum + this.blockNum; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    } else {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = this.allCount - this.blockNum; i < this.allCount; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push(this.allCount);
                    }
                }
            } else { // 若点击的是实际的数字页面，则进行页面切换
                this.currPage = val;
                this.$emit('handleCurrentChange', val);
            }
        },
        /** 上一页 */
        prePage() {
            if (this.currPage !== 1) {
                let index = this.currShowPage.indexOf(this.currPage);
                if (this.currShowPage[index - 1] !== '...') {
                    this.currPage--;
                } else {
                    if (this.currPage === this.allCount) {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = this.currPage - this.blockNum; i < this.allCount; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push(this.allCount);
                    } else if (this.currPage - this.blockNum <= 1){
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        for (let i = 1; i < this.blockNum; i++) {
                            this.currShowPage.push(i + 1);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    } else {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = this.currPage - this.blockNum; i <= this.currPage - 1; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    }
                    this.currPage--;
                }
            }
            this.$emit('handleCurrentChange', this.currPage);
        },
        /** 下一页 */
        afterPage() {
            let index = this.currShowPage.indexOf(this.currPage);
            if (this.currPage !== this.allCount) {
                if (this.currShowPage[index + 1] !== '...') {
                    this.currPage++;
                } else {
                    if (index === 0) {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        for (let i = 1; i <= this.blockNum; i++) {
                            this.currShowPage.push(i + 1);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    } else if (this.allCount - this.currPage >= this.blockNum){
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = this.currPage - this.blockNum + 2; i <= this.currPage + 1; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push('...');
                        this.currShowPage.push(this.allCount);
                    } else {
                        this.currShowPage = [];
                        this.currShowPage.push(1);
                        this.currShowPage.push('...');
                        for (let i = this.allCount - this.blockNum; i <= this.allCount - 1; i++) {
                            this.currShowPage.push(i);
                        }
                        this.currShowPage.push(this.allCount);
                    }
                    this.currPage++;
                }
            }
            this.$emit('handleCurrentChange', this.currPage);
        },
        /** 跳转页数 */
        GoPage() {
            this.currPage = Number(this.inputPage);
            this.$emit('handleCurrentChange', this.currPage);
        },
        PageSizeClick() {
            this.isShow = !this.isShow;
        },
        /** 每页显示条数变化时调取 */
        pageSizeChange(val) {
            this.$emit('handleSizeChange', val);
            this.getAllPage();
        },
        /** 获取所有的页数 */
        getAllPage() {
            this.currShowPage = [];
            this.allCount = Math.floor(this.total / this.currSize);
            let count = this.total % this.currSize;
            if (count !== 0) {
                this.allCount++;
            }
            if (this.allCount < 7) { // 页面数小于7的情况
                for (let i = 1; i <= this.allCount; i++) {
                    this.currShowPage.push(i);
                }
            } else { // 页面数大于等于7的情况
                this.currShowPage.push(1);
                this.currShowPage.push('...');
                let middleNum = Math.ceil(this.allCount / 2);
                let blockMiddle = Math.floor(this.blockNum / 2);
                for (let i = 0; i < this.blockNum; i++) {
                    this.currShowPage.push(middleNum + i - blockMiddle);
                }
                this.currShowPage.push('...');
                this.currShowPage.push(this.allCount);
            }
        }
    },
    mounted() {
        if (this.pageSizes.length !== 0) {
            this.currSize = this.pageSizes[0];
        }
        this.getAllPage();
    }
};
</script>

<style lang="scss">
    @import './commPage.scss';
</style>
