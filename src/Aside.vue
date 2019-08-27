<template>
  <div>
    <div>

      <!-- new connection button -->
      <div class="aside-new-connection-container">
        <el-button class="aside-new-connection" type="info" @click="dialogFormVisible = true" icon="el-icon-circle-plus">{{ $t('message.new_connection') }}</el-button>
      </div>

      <!-- new connection dialog -->
      <el-dialog :title="$t('message.new_connection')" :visible.sync="dialogFormVisible">
        <el-form :label-position="labelPosition" label-width="80px">
          <el-form-item :label="$t('message.new_connection_url')">
            <el-input v-model="newConnection.host" autocomplete="off" placeholder="127.0.0.1"></el-input>
          </el-form-item>

          <el-form-item :label="$t('message.new_connection_port')">
            <el-input v-model="newConnection.port" autocomplete="off" placeholder="6379"></el-input>
          </el-form-item>

          <el-form-item :label="$t('message.new_connection_auth')" >
            <el-input v-model="newConnection.auth" type="password" autocomplete="off"></el-input>
          </el-form-item>

          <el-form-item :label="$t('message.new_connection_name')">
            <el-input v-model="newConnection.name" autocomplete="off"></el-input>
          </el-form-item>

          <el-form-item label="">
            <el-checkbox v-model="sshOptionsShow" :label="$t('message.new_connection_ssh')"></el-checkbox>
          </el-form-item>

          <el-form v-if="sshOptionsShow" v-show="sshOptionsShow" label-width="80px">
            <el-form-item :label="$t('message.new_connection_url')">
              <el-input v-model="newConnection.sshOptions.host" autocomplete="off" 
                              placeholder="127.0.0.1"></el-input>
            </el-form-item>

            <el-form-item :label="$t('message.new_connection_port')">
              <el-input v-model="newConnection.sshOptions.port" autocomplete="off"></el-input>
            </el-form-item>

            <el-form-item :label="$t('message.new_connection_username')">
              <el-input v-model="newConnection.sshOptions.username" autocomplete="off"></el-input>
            </el-form-item>

            <el-form-item :label="$t('message.new_connection_auth')">
              <el-input v-model="newConnection.sshOptions.password" type="password" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>

        </el-form>

        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">{{ $t('el.messagebox.cancel') }}</el-button>
          <el-button type="primary" @click="addNewConnection">{{ $t('el.messagebox.confirm') }}</el-button>
        </div>
      </el-dialog>

    </div>

    <!-- connection list -->
    <div class="connections-list">
      <Connections ref="connections"></Connections>
    </div>

  </div>
</template>

<script>
import Connections from '@/components/Connections';
import storage from './storage';

export default {
  data() {
    return {
      dialogFormVisible: false,
      labelPosition: 'left',
      newConnection: {
        host: '',
        port: '',
        auth: '',
        name: '',
        sshOptions: {
          port: 22,
        }
      },
      sshOptionsShow: false,
    };
  },
  components: { Connections },
  methods: {
    addNewConnection() {
      const connection = JSON.parse(JSON.stringify(this.newConnection));

      !connection.host && (connection.host = '127.0.0.1');
      !connection.port && (connection.port = 6379);

      if (!this.sshOptionsShow || !connection.sshOptions.host) {
        delete connection.sshOptions;
      }

      if (storage.addConnection(connection) === false) {
        this.$message.error({
          message: this.$t('message.connection_exists'),
          duration: 2000,
        });

        return;
      }

      this.dialogFormVisible = false;
      this.$refs.connections.initConnections();
    },
  },
};
</script>

<style type="text/css">
  .aside-new-connection-container {
    margin-right: 8px;
  }
  .aside-new-connection {
    width: 100%;
  }
</style>
