/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
<template>
  <div class="user-def-params-model">
    <div class="select-listpp"
         v-for="(item,$index) in targetTableDependList"
         :key="item.id"
         @click="_getIndex($index)">
      <m-datasource
        ref="refDt"
        @on-dsData="_onDtData($index)"
        :supportType="targetDbTypeByDependList"
        :data="{ type:targetTableDependList[$index].targetDbType,datasource:targetTableDependList[$index].targetDb }">
      </m-datasource>
      <x-input
        type="textarea"
        v-model="targetTableDependList[$index].targetTable"
        :placeholder="$t('Please enter the table of target')"
        autocomplete="off"
        :disabled="isDetails">
      </x-input>
      <span class="lt-add">
        <a href="javascript:" style="color:red;" @click="!isDetails && _removeUdp($index)" >
          <em class="ans-icon-trash" :class="_isDetails" data-toggle="tooltip" :title="$t('delete')" ></em>
        </a>
      </span>
      <span class="add" v-if="$index === (targetTableDependList.length - 1)">
        <a href="javascript:" @click="!isDetails && _addUdp()" >
          <em class="iconfont ans-icon-increase" :class="_isDetails" data-toggle="tooltip" :title="$t('Add')"></em>
        </a>
      </span>
    </div>
    <span class="add-dp" v-if="!targetTableDependList.length">
      <a href="javascript:" @click="!isDetails && _addUdp()" >
        <em class="iconfont ans-icon-increase" :class="_isDetails" data-toggle="tooltip" :title="$t('Add')"></em>
      </a>
    </span>
  </div>
</template>
<script>
  import _ from 'lodash'
  import i18n from '@/module/i18n'
  import { targetDbTypeByDependList } from './common'
  import disabledState from '@/module/mixin/disabledState'
  export default {
    name: 'user-def-params',
    data () {
      return {
        // target db type list (check depend)
        targetDbTypeByDependList: targetDbTypeByDependList,
        // Increased data
        targetTableDependList: [],
        // Current execution index
        targetTableDependsIndex: null
      }
    },
    mixins: [disabledState],
    props: {
      udpList: Array
    },
    methods: {
      /**
       * Current index
       */
      _getIndex (index) {
        this.targetTableDependsIndex = index
      },
      /**
       * return data target
       */
      _onDtData (o, index) {
        this.targetTableDependList[index].targetDbType = o.type
        this.targetTableDependList[index].targetDb = o.datasource
      },
      /**
       * delete item
       */
      _removeUdp (index) {
        this.targetTableDependList.splice(index, 1)
        this._verifProp('value')
      },
      /**
       * add
       */
      _addUdp () {
        this.targetTableDependList.push({
          targetDbType: '',
          targetDb: '',
          targetTable: '',
        })
      },
      /**
       * Verify that the value exists or is empty
       */
      _verifTargetTable (type) {
        let flag = true
        _.map(this.targetTableDependList, v => {
          if (!v.targetTable) {
            flag = false
          }
        })
        if (!flag) {
          if (!type) {
            this.$message.warning(`${i18n.$t('Please enter a Target Table(required)')}`)
          }
          return false
        }
        this.$emit('on-target-table-depends', _.cloneDeep(this.targetTableDependList))
        return true
      }
    },
    watch: {
      // Monitor data changes
      udpList () {
        this.targetTableDependList = this.udpList
      }
    },
    created () {
      this.targetTableDependList = this.udpList
    },
    computed: {
      inputStyle () {
        return `width:${this.hide ? 160 : 262}px`
      }
    },
    mounted () {
    },
    components: { }
  }
</script>

<style lang="scss" rel="stylesheet/scss">
  .user-def-params-model {
    .select-listpp {
      margin-bottom: 6px;
      .lt-add {
        padding-left: 4px;
        a {
          .iconfont {
            font-size: 18px;
            vertical-align: middle;
            margin-bottom: -2px;
            display: inline-block;
          }
        }
      }
    }
    .add {
      a {
        color: #000;
        .iconfont {
          font-size: 16px;
          vertical-align: middle;
          display: inline-block;
          margin-top: -5px;
        }
      }
    }
    .add-dp{
      a {
        color: #0097e0;
        .iconfont {
          font-size: 18px;
          vertical-align: middle;
          display: inline-block;
          margin-top: 2px;
        }
      }
    }
  }
</style>
