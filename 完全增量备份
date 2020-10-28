import datetime
import tarfile
import pickle
from FuncTions import md5check as fm
from FuncTions import getbase as fb


# 完全备份
def fullBackup():
    source_dir = r"c:\2345Downloads"

    # 定义保存文件校验码字典的文件
    md5_file = r"c:\2345Downloads\backup\files.md5"

    current_time = datetime.datetime.now().strftime("%Y_%m_%d_%H_%M_%S")
    full_tar_file_name = r"c:\2345Downloads\backup\data_%s.tar.gz" % current_time
    full_tar_obj = tarfile.open(full_tar_file_name, mode="w:gz")

    # {"文件名称":"文件MD5"}
    file_md5_dict = {}

    source_file_list = fb.listFile(source_dir)
    # 遍历列表，获取文件名称
    for file_name in source_file_list:
        # 备份每个文件
        full_tar_obj.add(file_name)
        # 获取文件的MD5校验码，保存到字典
        source_file_md5 = fm.fileMD5(file_name)
        file_md5_dict[file_name] = source_file_md5

    full_tar_obj.close()

    # 保存字典
    with open(md5_file, mode="wb") as fobj:
        pickle.dump(file_md5_dict, fobj)


# 增量备份
def increBackup():
    source_dir = r"c:\2345Downloads"

    # 创建增量备份的压缩包
    current_time = datetime.datetime.now().strftime("%Y_%m_%d_%H_%M_%S")
    incr_tar_file_name = r"c:\2345Downloads\backup\data_incre_%s.tar.gz" % current_time
    incre_tar_obj = tarfile.open(incr_tar_file_name, mode="w:gz")

    # 获取完全备份的字典
    md5_file = r"c:\2345Downloads\backup\files.md5"
    with open(md5_file, mode="rb") as fobj:
        file_md5_dict = pickle.load(fobj)

    # 检测文件变化，做增量备份
    source_file_list = fb.listFile(source_dir)
    for file_name in source_file_list:
        # 判断新文件, 备份文件
        if file_name not in file_md5_dict.keys():
            incre_tar_obj.add(file_name)
            # 更新字典，在字典中添加新文件的校验码
            new_md5 = fm.fileMD5(file_name)
            file_md5_dict[file_name] = new_md5
        else:
            # 获取同名存在的文件，判断文件内容的变化
            new_md5 = fm.fileMD5(file_name)
            old_md5 = file_md5_dict.get(file_name)
            if new_md5 != old_md5:
                incre_tar_obj.add(file_name)
                # 更新原有文件的校验码
                file_md5_dict[file_name] = new_md5
    incre_tar_obj.close()

    # 更新硬盘中的字典
    with open(md5_file, mode="wb") as fobj:
        pickle.dump(file_md5_dict, fobj)


if __name__ == '__main__':
     # 周一做完全备份
     import time
     day = time.strftime("%a")
     if day == "Mon":
         fullBackup()
     else:
         increBackup()
          
