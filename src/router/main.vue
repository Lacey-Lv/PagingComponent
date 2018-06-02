<template>
    <div class="test-paging">
        <div class="input-info">
            <span>输入总条数:</span><input placeholder="输入总条数" v-model="totalCount" type="number" />
            <span>输入中间block个数:</span><input placeholder="输入中间显示的个数, 小于5" v-model="blockNum" type="number" max="5" min="1" /><i>最大为5，输入小于5的数字</i>
        </div>
        <paging :total="totalCount" :blockNum="blockNum" @handleCurrentChange="handleCurrentChange" @handleSizeChange="handleSizeChange"></paging>
    </div>
</template>

<script>
import paging from 'components/commPage.vue';
export default {
    data() {
        return {
            totalCount: 10,
            blockNum: 5
        };
    },
    components: {
        paging
    },
    methods: {
        handleCurrentChange(val) {
            console.log('当前页数', val);
        },
        handleSizeChange(val) {
            console.log('每页显示条数', val);
        }
    }
}
</script>

<style lang="scss">
    @import './main.scss';
</style>


