<template>
    <div class="header_">
        <el-menu
                :defaultActive="$route.path"
                ref="menus"
                class="elMenu"
                mode="horizontal"
                :router='true'>
            <div class="img" style="border-bottom: 2px solid transparent;">
                <img src="../../static/imgs/softIcon.png">
            </div>
            <el-menu-item index="/main/recommend">首页</el-menu-item>
            <el-menu-item index="/main/coursesPage">课程</el-menu-item>
            <el-menu-item index="/main/articlesPage">文章</el-menu-item>
            <el-menu-item index="/main/questionPage">问答</el-menu-item>
            <searchItem style="align-self: center;width: 300px;cursor: text;"> </searchItem>
            <div class="img">
                <avatarInfo style="color: #adafb3"></avatarInfo>
            </div>
        </el-menu>
    </div>
</template>

<script>
    import avatarInfo from './info.vue';
    import searchItem from './search.vue';
    export default {
        components: {
            avatarInfo,
            searchItem
        }
    }
</script>

<style scoped lang="scss">
    .header_ {
        position: fixed;
        left: 0;
        top: 0;
        width: 100%;
        z-index: 100;
        .elMenu{
            display: flex;
            justify-content: space-between;
            flex-wrap: nowrap;
        }
        .img {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 61px;
            box-sizing: border-box;
            padding: 0 20px;
            outline: none;
            border-color: #b0b0b0;
            img {
                width: 45px;
                height: 45px;
            }
        }
    }
</style>

