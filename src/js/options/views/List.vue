<template>
    <div class="list">
        <el-main>
            <div class="title">规则列表</div>
            <div class="tip">
                <p>更多规则支持中，快来<a target="_blank" href="https://docs.rsshub.app/joinus/">参与我们</a>吧！</p>
                <p>{{ time }}前更新</p>
            </div>
            <div class="content" v-loading="loading">
                <el-collapse accordion>
                    <el-collapse-item v-for="(rule, domain) in rules" :key="domain" :title="rule._name + ' - ' + domain">
                        <div v-for="(subrule, subdomain) in rule" v-if="subdomain[0] !== '_'" :key="subdomain">
                            <p v-for="subsubrule in subrule" :key="subsubrule.title">
                                <a target="_blank" :href="subsubrule.docs">{{ subsubrule.title }}</a>
                            </p>
                        </div>
                    </el-collapse-item>
                </el-collapse>
                <div class="debug">
                    <div class="tip">
                        <p>此处用于开发中的规则调试，非战斗人员请迅速撤离</p>
                        <p>编辑内容随时可能被自动更新的规则覆盖，请保证本地有备份</p>
                        <p>使用 设置-立即更新 可以立即恢复远程规则</p>
                    </div>
                    <el-input
                        type="textarea"
                        :rows="100"
                        placeholder="请输入内容"
                        v-model="rulesText"
                        @change="updateRules">
                    </el-input>
                </div>
            </div>
        </el-main>
    </div>
</template>

<script>
import { getRules, getRulesDate, updateRules } from '../../common/rules';
import { secondToTime } from '../../common/utils';

export default {
    name: 'List',
    data: () => ({
        loading: true,
        rules: {},
        time: '',
        rulesText: '',
    }),
    created() {
        getRulesDate((date) => {
            let second = (+new Date - +date) / 1000;
            this.time = secondToTime(second);
            this.refreshTime();
        });
        getRules((rules, text) => {
            this.rules = rules;
            this.rulesText = text;
            this.loading = false;
        });
    },
    methods: {
        refreshTime() {
            getRulesDate((date) => {
                this.time = secondToTime((+new Date - +date) / 1000);
                setTimeout(() => {
                    this.refreshTime();
                }, 1000);
            });
        },
        updateRules() {
            updateRules(this.rulesText, () => {
                this.$message({
                    message: '保存成功',
                    type: 'success'
                });
            });
        },
    }
}
</script>

<style lang="less" scoped>
a {
    text-decoration: none;
    color: #f5712c;
}

.list {
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
    padding: 40px 10px;
}

.title {
    font-size: 25px;
    font-weight: bold;
    border-bottom: 1px solid #e6e6e6;
    padding-bottom: 10px;
    color: #f5712c;
}

.tip {
    font-size: 14px;
    margin: 20px 0;
    color: #555;
}

.content {
    margin-top: 20px;
    color: #222;
}

.debug {
    margin: 200px 0;
}
</style>
