import shutil
import os
import time
import exifread


class ReadFailException(Exception):
    pass


def getOriginalDate(filename):
    try:
        fd = open(filename, 'rb')  # 以二进制读取
    except:
        raise ReadFailException("unopen file[%s]\n" % filename)
        print("Unable to open file:\"{0}\".".format(filename))
    data = exifread.process_file(fd)
    if data:
        try:
            t = data['EXIF DateTimeOriginal']  # 查找字典
            temp = str(t).replace(":", "年", 1).replace(":", "月", 1)[:8]
            # return str(t).replace(":", ".")[:7]
            return temp
        except:
            pass

    # 尝试是否能从文件名获取拍摄时间
    if(filename[2:5] == "IMG" or filename[2:5] == "VID"):
        return filename[6:10]+"年"+filename[10:12]+"月"
    elif(filename[2:12] == "Screenshot"):
        return filename[13:17]+"年"+filename[18:20]+"月"
    elif(filename[2:3] == "V"):
        return "20"+filename[3:5]+"年"+filename[6:8]+"月"

    # 如果没有拍摄日期信息
    state = os.stat(filename)
    return time.strftime("%Y年%m月", time.localtime(state[-2]))


def classifyPictures(path):

    for root, dirs, files in os.walk(path, True):
        # os.walk()方法用于通过在目录树中游走输出在目录中的文件名
        # root:当前正在遍历的这个文件夹的本身的地址
        # dirs是一个 list ,内容是该文件夹中所有的目录的名字(不包括子目录)
        # files同样是 list ,内容是该文件夹中所有的文件(不包括子目录)
        print("当前文件夹地址: "+str(root))
        print("所有子文件夹: "+str(dirs))
        dirs[:] = []  # 清除dirs
        for filename in files:
            # 把目录root和文件名filename合成一个路径
            filename = os.path.join(root, filename)

            # 分割路径，返回路径名f和文件扩展名e(类型为字符串)的元组
            f, e = os.path.splitext(filename)

            # 通过后缀名检测图片和视频文件，如果不是则跳过
            if e.lower() not in ('.jpg', '.jpeg', '.png', '.dng', 'bmp', '.gif', '.mp4', '.3gp', '.mov'):
                continue

            # 开始分类
            t = ""
            try:
                t = getOriginalDate(filename)
            except Exception as e:
                print(e)
                continue

            info = "文件名: " + filename[2:] + "  拍摄时间: " + t + "  "
            pwd = root + '\\' + t
            dst = pwd + '\\' + filename[2:]

            # 创建不存在的目标目录
            if not os.path.exists(pwd):
                os.mkdir(pwd)
            print(info+"目标文件夹: "+dst[:10])

            # 移动文件到目标目录
            # shutil.copy2(filename, dst)
            # os.remove(filename)
            shutil.move(filename, dst)


if __name__ == "__main__":
    path = "."
    classifyPictures(path)
