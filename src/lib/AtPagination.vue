<template>
    <div>
        <div v-show="pagination.items.length>0" class="pagination-content">
            <slot :items="pagination.items" :result-data="resultData"></slot>
        </div>
        <div v-show="!pagination.items.length" class="empty-content">
            <div>暂无数据</div>
        </div>
        <div class="pagination-outer-wrapper">
            <div class="pagination">
                <div class="features-wrapper">
                    <span class="page-index">{{pagination.page}}/{{pagination.pageCount}}</span>
                    <div class="page-btns">
                        <span class="page-btn prev-btn" v-bind:class="{'disabled-btn': pagination.page <= 1, 'index-on': pagination.page >= pagination.pageCount}" @click="goSpecificPage(-1)">&lt;</span>
                        <i class="line-vertical"></i>
                        <span class="page-btn next-btn" v-bind:class="{'disabled-btn': pagination.page >= pagination.pageCount}" @click="goSpecificPage(1)">&gt;</span>
                    </div>
                </div>
                <div class="features-wrapper">
                    每页<v-select id="sizeSelectBox" :searchable="false" :clearable="false" :options="pagination.sizes" v-model="pagination.size" @input="reload()"></v-select>
                </div>
                <div class="features-wrapper">
                    到第<input type="number" class="page-num"  min="1" max="999" v-model="pagination.pageTo" v-on:keyup.enter="goSpecificPage(0)">页
                    <button class="btn page-confirm" @click="goSpecificPage(0)">确定</button>
                </div>
            </div>
        </div>
    </div>


</template>
<script>
    import vSelect from 'vue-select'
    export default {
        name: 'AtPagination',
        components:{vSelect},
        props: {
            queryUrl: {
                type: String
            },
            queryParams: {
                type: Object
            },
            data: {
                type: Object
            }
        },
        data: function () {
            return {
                pagination: {
                    pageTo        : 1,
                    page: 1,
                    size: 20,
                    pageCount: 1,
                    sizes: [10, 20, 50, 100, 150],
                    totalItemCount: 0,
                    items: []
                },
                resultData:{}
            }
        },
        created: function () {
            Object.assign(this.pagination, this.data || {})
            this.reload();
        },
        methods: {
            goSpecificPage:function(type){
                if (type === 1) {
                    this.pagination.page++
                    if (this.pagination.page > this.pagination.pageCount) {
                        this.pagination.page  = this.pagination.pageCount
                        return
                    }
                } else if (type === -1) {
                    this.pagination.page--
                    if (this.pagination.page <= 0) {
                        this.pagination.page = 1
                        return
                    }
                } else if (type === 0) {
                    this.pagination.page   = this.pagination.pageTo > this.pagination.pageCount ? this.pagination.pageCount : this.pagination.pageTo
                    this.pagination.pageTo = this.pagination.page
                }
                this.reload();
            },
            reload: function (p) {
                if (p) {
                    this.pagination.page = p
                }
                new Promise(resolve => {
                    if (this.queryUrl) {
                        axios({
                            method: 'GET',
                            url: this.queryUrl || '',
                            params: Object.assign({}, this.queryParams, {
                                page: this.pagination.page,
                                size: this.pagination.size
                            })
                        }).then((res) => {
                            if (res && res.data && res.data.data) {
                                let data = res.data.data
                                this.resultData = data;
                                this.pagination.pageCount = data.pageCount;
                                this.pagination.totalItemCount = data.totalItemCount || 0;
                                this.pagination.items = data.items || [];
                                resolve(this.pagination)
                            }
                        })
                    } else {
                        let resp = '{"errorCode":null,"errorMsg":null,"data":{"pageCount":12,"page":1,"names":["等待投放宝贝","等待移除宝贝"],"size":10,"items":[{"sellerId":68486,"releaseCount":269,"removeCount":782},{"sellerId":206383,"releaseCount":726,"removeCount":236},{"sellerId":27584,"releaseCount":264,"removeCount":165},{"sellerId":10302,"releaseCount":593,"removeCount":563},{"sellerId":105869,"releaseCount":706,"removeCount":853},{"sellerId":31965,"releaseCount":333,"removeCount":773},{"sellerId":8233,"releaseCount":109,"removeCount":412},{"sellerId":206052,"releaseCount":751,"removeCount":123},{"sellerId":59829,"releaseCount":998,"removeCount":992},{"sellerId":137120,"releaseCount":767,"removeCount":939}],"totalItemCount":120},"success":true}';
                        let res = JSON.parse(resp);
                        if (res && res.data ) {
                            let data = res.data
                            this.pagination.pageCount = data.pageCount;
                            this.pagination.totalItemCount = data.totalItemCount || 0;
                            this.pagination.items = data.items || [];
                            resolve(this.pagination)
                        }
                    }

                }).then(data => {
                    this.$emit('update:data', data)
                    console.log(data)
                })
            }
        }
    }
</script>
<style lang="scss" >

    .empty-content{
        width: 100%;
        height: 200px;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    @import "~vue-select/src/scss/vue-select.scss";
    .pagination-outer-wrapper {
        display: flex;
        justify-content: flex-end;

        .pagination {
            height: 34px;
            min-width: 420px;
            display: flex;
            float: inherit;
            flex-direction: row;
            align-items: center;
            justify-content: space-between;
            font-family: 'MicrosoftYaHei';
            font-size: 14px;
            color: #666;

            .features-wrapper {

                #sizeSelectBox{
                    width: 100px;
                }
                display: flex;
                flex-direction: row;
                justify-content: space-between;
                align-items: center;

                .page-btns {
                    display: flex;
                    width: 70px;
                    height: 34px;
                    font-size: 12px;
                    flex-direction: row;
                    justify-content: center;
                    margin-left: 12px;
                    color: #888;

                    .page-btn {
                        width: 32px;
                        height: 34px;
                        text-align: center;
                        line-height: 34px;
                        border: 1px solid #888;
                        cursor: pointer;
                        top: 0;
                        z-index: 0;
                        background-color: white;

                        &:hover {
                            color: #409EFF;
                            border-color: #409EFF;
                        }
                    }

                    .prev-btn {
                        border-radius: 4px 0 0 4px;

                        &:hover {
                            z-index: 1;
                        }
                    }

                    .next-btn {
                        border-radius: 0 4px 4px 0;
                        margin-left: -1px;
                    }

                    .disabled-btn {
                        color: #ddd;
                        border-color: #ddd;

                        &:hover {
                            color: #ddd;
                            border-color: #ddd;
                            z-index: 0;
                        }
                    }

                    .index-on {
                        z-index: 1;
                    }
                }

                div.size-select {
                    width: 61px;
                    height: 34px;
                    border-color: #ddd;
                    border-radius: 4px;
                    margin: 0 5px;

                    button.btn {
                        height: 34px;
                    }

                    .dropdown-menu {
                        z-index: 1001;
                    }
                }

                input[type=text].page-num, input[type=number].page-num {
                    line-height: 20px;
                    font-size: 14px;
                    height: 8px;
                    width: 41px;
                    padding: 12px 6px;
                    border-radius: 4px;
                    outline: none;
                    margin: 0 5px;
                    text-align: center;
                }

                .page-confirm {
                    width: 50px;
                    height: 34px;
                    margin-left: 10px;
                    padding: 0;
                    //line-height: 34px;
                    line-height: 32px;
                    text-align: center;
                    background: #fff;
                    color: #333;
                    border: 1px solid #ddd;

                    &:hover {
                        color: #409EFF;
                        border-color: #409EFF;
                    }
                }
            }
        }
    }
</style>
