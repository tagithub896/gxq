<template>
		<Layout>
			<Content>
				<Breadcrumb>
					<BreadcrumbItem>统一监控平台</BreadcrumbItem>
					<BreadcrumbItem>预警处理台账</BreadcrumbItem>
				</Breadcrumb>
                <Layout class="ivu-layout-has-sider">
                    <Col span="3">
                        <Tree :data="position"></Tree>
                    </Col>
                    <Col span="20" style="margin-left:10px;">
                        <Menu active-name="1" style="width:100%;height:100%;">
                            <MenuGroup title="人员列表">
                                <Card>
                                    <Form ref="formValidate" inline :label-width="90">
                                        <FormItem label="关键字">
                                            <Input type="text" placeholder="请输入姓名、性别、职位"></Input>
                                        </FormItem>
                                        <FormItem :label-width="20">
                                            <Button type="primary">查询</Button>
                                        </FormItem>
                                    </Form>
                                    <Table border ref="selection" :columns="columns" :data="data"></Table>
                                    <div class="bottom-bar">
                                        <Page :total="100" show-sizer show-elevator show-total></Page>
                                    </div>
                                </Card>
                            </MenuGroup>
                        </Menu>
                    </Col>
                </Layout>
			</Content>
			<Modal @on-visible-change="openModal" v-model="modal" title="详情" width="60%" :mask-closable="false">
				<div style="text-align:center">
                    <Form ref="evalData" :model="evalData" :rules="ruleValidate" :label-width="70">
                        <Row :gutter="40">
                            <Col span="12">
                                <FormItem label="姓名" :label-width="85" label-position="left" prop="name">
                                    <Input v-model="evalData.name" disabled></Input>
                                </FormItem>
                            </Col>
                            <Col span="9">
                                <FormItem label="性别" prop="gender">
                                    <RadioGroup v-model="evalData.gender" label-position="left">
                                        <Radio label="男"></Radio>
                                        <Radio label="女"></Radio>
                                    </RadioGroup>
                                </FormItem>
                            </Col>
                            <Col span="12">
                                <FormItem label="职位" :label-width="85" label-position="left" prop="position">
                                    <Select v-model="evalData.position" disabled>
                                        <Option value="组长">组长</Option>
                                        <Option value="副组长">副组长</Option>
                                        <Option value="客服">客服</Option>
                                        <Option value="技术支持工程师">技术支持工程师</Option>
                                    </Select>
                                </FormItem>
                            </Col>
                            <Col span="11">
                                <FormItem label="电话" label-position="left" prop="phone">
                                    <Input v-model="evalData.phone"></Input>
                                </FormItem>
                            </Col>
                            <Col span="22">
                                <FormItem label="主要负责系统" :label-width="100" label-position="left" prop="system">
                                    <Input v-model="evalData.system" type="text"></Input>
                                </FormItem>
                            </Col>
                            <Col span="22">
                                <FormItem label="主要工作内容" :label-width="100" label-position="left" prop="desc">
                                    <Input v-model="evalData.desc" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入工作内容..." style="resize:none;"></Input>
                                </FormItem>
                            </Col>
                            <Col span="24">
                                <FormItem class="act-btn-group">
                                    <Button type="primary" @click="handleSubmit('evalData')" size="large">确定</Button>
                                    <Button type="default" @click="closeModal" size="large">取消</Button>
                                </FormItem>
                            </Col>
                        </Row>
                    </Form>
                </div>
			</Modal>
		</Layout>
</template>

<script>
export default {
    data() {
        return {
            modal: false,
            evalData:{
                name:'男',
                gender:'组长',
                position:'',
                phone:'',
                system:'',
                desc:''
            },
            position:[
                {
                    title: 'XX单位',
                    expand: true,
                    children: [
                        {
                            title: '组长',
                            expand: true,
                        },
                        {
                            title: '副组长',
                            expand: true,
                        },
                        {
                            title: '客服',
                            expand: true,
                        },
                        {
                            title: '技术支持工程师',
                            expand: true,
                        },
                    ]
                },
                {
                    title: 'XX单位',
                    expand: true,
                    children: [
                        {
                            title: '组长',
                            expand: true,
                        },
                        {
                            title: '副组长',
                            expand: true,
                        },
                        {
                            title: '客服',
                            expand: true,
                        },
                        {
                            title: '技术支持工程师',
                            expand: true,
                        },
                    ]
                }
            ],
            ruleValidate: {
                name:[
                    { required:true,message:'姓名不能为空' }
                ],
                gender:[
                    { required:true,message:'选项不能为空' }
                ],
                position:[
                    { required:true,message:'选项不能为空' }
                ],
                phone:[
                    { required:true,message:'电话号码不能为空' }
                ],
                system:[
                    { required:true,message:'系统描述不能为空' }
                ],
                desc:[
                    { required:true,message:'工作内容不能为空' }
                ]
            },
            columns: [{
                    type: 'index',
                    title: '序号',
                    width: 80,
                    align: 'center'
                },
                {
                    title: '编号',
                    key: 's1'
                },
                {
                    title: '姓名',
                    width: 100,
                    key: 's2'
                },
                {
                    title: '性别',
                    width: 80,
                    key: 's3'
                },
                {
                    title: '职位',
                    width: 150,
                    key: 's4'
                },
                {
                    title: '电话',
                    width: 150,
                    key: 's5'
                },
                {
                    title: '主要工作内容',
                    key: 's6'
                },
                {
                    title: '主要负责系统',
                    key: 's7'
                },
                {
                    title: '操作',
                    key: 'act',
                    align: 'center',
                    render: (h, params) => {
                        return h('div', [
                            h('Button', {
                                props: {
                                    type: 'primary',
                                    size: 'small'
                                },
                                style: {
                                    marginRight: '15px'
                                },
                                on: {
                                    click: () => {
                                        this.modal = true;												
                                    }
                                }
                            }, '编辑'),
                            h('Button', {
                                props: {
                                    type: 'primary',
                                    size: 'small'
                                },
                                on: {
                                    click: () => {
                                        this.modal = true;
                                    }
                                }
                            }, '详情')
                        ]);
                    }
                },
            ],
            data: []
        }
    },
    components:{
    },
    methods:{
        openModal(status) {
            if(status){
                document.querySelector('.ivu-modal-footer').style.display = 'none';
            }else{
                this.$refs['evalData'].resetFields();
            }
        },
        closeCallBack(status) {
            if(!status) {
                this.$refs['interfaceData'].resetFields();
            }
        },
        closeModal() {
            this.modal = false;
        },
        addBackup(){
            this.modal = true;
        },
        handleSubmit() {
            console.log("确定");
            this.$refs['evalData'].validate((valid) => {
                console.log(valid);
                if(valid) {
                    console.log("验证成功");
                } else {
                    console.log(this.$refs['modal'])
                    this.$Message.error('表单验证失败!');
                }
            })
        },
    }
}
</script>

<style type="text/css" scoped>
.wAuto{
	width: 100%;
}
.flow{
	margin-top: 20px ;
}
</style>