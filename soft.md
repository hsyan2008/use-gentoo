# 其他推荐软件
* notepad++的替代品scite
    * 安装
    
            emerge -av scite
    * 配置(options->open user option file)
    
            position.left=0
            position.top=0
            position.width=-1
            position.height=-1
            statusbar.visible=1
            code.page=65001
            tabsize=4
            indent.size=4
            autocompleteword.automatic=1
            # Go stuff
            lexer.*.go=cpp
            use.tabs.*.go=1
            tab.size.*.go=4
            indent.size.*.go=4
            # copied straight from the spec, added primitive types
            keywords.*.go= \
            break default func interface select \
            case defer go map struct \
            chan else goto package switch \
            const fallthrough if range type \
            continue for import return var \
            bool int int8 int16 int32 int64 \
            byte uint uint8 uint16 uint32 uint64 uintptr \
            float float32 float64 string nil true false