<template>
  <div @click="handleClick" class="mint-directive-else">
    <slot></slot>
  </div>
</template>
<script>
    /**
     * @noteType component
     * @name DirectiveElse
     * @tagName mt-directive-else
     * @desc else容器
     * @type container|directive
     * @directiveName v-else
     * @wrapClassName mint-directive-else
     * @html
     * <mt-directive-else></mt-directive-else>
     */
    export default {
        name: 'mt-directive-else',
        methods: {
            handleClick(evt) {
                this.$emit('click', evt);
            }
        },
        props: {}
    };

</script>
<style lang="css">
  @component-namespace mint {
    @component directive-else {
        position: relative;

        &:after{
          content: "";
          display: table;
          clear: both;
         }
    }
  }

</style>
